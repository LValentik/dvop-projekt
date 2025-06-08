<template>
  <div class="register-page">
    <h1>Register</h1>
    <UForm :state="form" @submit="handleRegister" class="space-y-4">
      <UFormField label="Username" name="username" class="w-full">
        <UInput v-model="form.username" id="username" required class="w-full" />
      </UFormField>
      <UFormField label="Email" name="email" class="w-full">
        <UInput v-model="form.email" id="email" type="email" required class="w-full" />
      </UFormField>
      <UFormField label="Password" name="password" class="w-full">
        <UInput v-model="form.password" id="password" type="password" required class="w-full" />
      </UFormField>
      <UButton type="submit" color="primary" class="w-full">
        Register
      </UButton>
      <div v-if="error" class="text-red-600">{{ error }}</div>
      <div v-if="success" class="text-green-600">Registration successful!</div>
    </UForm>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const form = ref({
  username: '',
  email: '',
  password: ''
})

const error = ref('')
const success = ref(false)

async function handleRegister() {
  error.value = ''
  success.value = false
  try {
    const response = await fetch('http://localhost:1337/api/auth/local/register', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        username: form.value.username,
        email: form.value.email,
        password: form.value.password
      })
    })
    const data = await response.json()
    if (data.jwt) {
      document.cookie = `jwt=${data.jwt}; path=/; max-age=${60 * 60 * 24 * 7}`
      success.value = true
      form.value = { username: '', email: '', password: '' }
      // Optionally redirect or update UI
    } else {
      error.value = data.message || 'Registration failed'
    }
  } catch (err) {
    error.value = 'Registration error'
  }
}
</script>

<style scoped>
.register-page {
  max-width: 400px;
  margin: 2rem auto;
  padding: 2rem;
  border: 1px solid #ddd;
  border-radius: 8px;
}
.register-page h1 {
  text-align: center;
  margin-bottom: 1.5rem;
}
</style>