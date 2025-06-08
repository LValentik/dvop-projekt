<template>
  <div>
    <div
      v-if="requireLogin && !userId"
      class="bg-yellow-100 border border-yellow-300 text-yellow-800 px-4 py-3 rounded mb-6 flex items-center justify-between"
    >
      <span>You must be logged in to submit this form.</span>
      <UButton color="primary" to="/login" class="ml-4">Login</UButton>
    </div>
    <UForm
      v-if="!requireLogin || userId"
      :state="formState"
      @submit="submitForm"
      class="max-w-xl mx-auto mt-8 space-y-4"
    >
      <template v-for="field in fields" :key="field.Name">
        <UFormField :label="field.Label" :name="field.Name" class="w-full">
          <UInput
            v-if="field.Type === 'text' || field.Type === 'email' || field.Type === 'number'"
            v-model="formState[field.Name]"
            :id="field.Name"
            :type="field.Type"
            :required="field.required"
            class="w-full"
            :min="field.Type === 'number' ? 0 : undefined"
          />
          <USelect
            v-else-if="field.Type === 'select'"
            v-model="formState[field.Name]"
            :id="field.Name"
            :required="field.required"
            class="w-full"
            :options="field.options || []"
          />
          <!-- Add more field types as needed -->
        </UFormField>
      </template>
      <UButton type="submit" :loading="loading" color="primary" class="w-full">
        {{ loading ? 'Submitting...' : 'Submit' }}
      </UButton>
      <div v-if="error" class="text-red-600">{{ error }}</div>
      <div v-if="success" class="text-green-600">Form submitted successfully!</div>
    </UForm>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  formId: { type: [String, Number], required: true }, // Strapi form id
  fields: { type: Array, required: true }, // Array of field definitions from Strapi
  requireLogin: { type: Boolean, default: false }
})

const formState = ref({})
const loading = ref(false)
const error = ref('')
const success = ref(false)
const userId = ref(null)

// Initialize form state with empty values
onMounted(() => {
  props.fields.forEach(field => {
    formState.value[field.Name] = ''
  })
  // Optionally get user id from JWT/cookie if needed
  const jwt = document.cookie.split('; ').find(row => row.startsWith('jwt='))
  if (jwt) {
    fetch('http://localhost:1337/api/users/me', {
      headers: { Authorization: `Bearer ${jwt.split('=')[1]}` }
    })
      .then(res => res.ok ? res.json() : null)
      .then(user => { if (user) userId.value = user.id })
  }
})

async function submitForm() {
  loading.value = true
  error.value = ''
  success.value = false

  // Required fields check
  for (const field of props.fields) {
    if (field.required && !formState.value[field.Name]) {
      error.value = `Field "${field.Label}" is required.`
      loading.value = false
      return
    }
  }

  // If login is required and user is not logged in
  if (props.requireLogin && !userId.value) {
    error.value = 'You must be logged in to submit this form.'
    loading.value = false
    return
  }

  try {
    const payload = {
      data: {
        form: props.formId,
        data: { ...formState.value }
      }
    }
    if (userId.value) payload.data.users_permissions_user = userId.value

    const response = await fetch('http://localhost:1337/api/form-submissions', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    })
    if (!response.ok) {
      throw new Error('Failed to submit form')
    }
    success.value = true
    // Reset form
    Object.keys(formState.value).forEach(k => (formState.value[k] = ''))
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}
</script>