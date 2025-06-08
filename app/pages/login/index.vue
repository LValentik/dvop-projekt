<template>
<Navbar />
  <div class="login-page">
    <h1>Login</h1>
    <UForm :state="form" @submit="handleLogin" class="space-y-4">
      <UFormField label="Username" name="username" class="w-full">
        <UInput v-model="form.username" id="username" required class="w-full" />
      </UFormField>
      <UFormField label="Password" name="password" class="w-full">
        <UInput v-model="form.password" id="password" type="password" required class="w-full" />
      </UFormField>
      <UButton type="submit" color="primary" class="w-full">
        Login
      </UButton>
    </UForm>
    <div class="my-6 text-center text-gray-500">or</div>
    <UButton
      color="red"
      class="w-full"
      @click="loginWithGoogle"
    >
      Login with Google
    </UButton>
    <div class="mt-6 text-center">
      <NuxtLink to="/register" class="text-blue-600 hover:underline">
        Don't have an account? Register here
      </NuxtLink>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Navbar from '~/components/Navbar.vue'

const form = ref({
  username: '',
  password: ''
})

async function handleLogin() {
  try {
    const response = await fetch('http://localhost:1337/api/auth/local', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        identifier: form.value.username,
        password: form.value.password
      })
    })
    const data = await response.json()
    if (data.jwt) {
      document.cookie = `jwt=${data.jwt}; path=/; max-age=${60 * 60 * 24 * 7}`
      alert('Login successful!')
      // Redirect or update UI as needed
    } else {
      alert(data.message || 'Login failed')
    }
  } catch (err) {
    alert('Login error')
  }
}

function loginWithGoogle() {
  window.location.href = 'http://localhost:1337/api/connect/google'
}
</script>

<style scoped>
.login-page {
  max-width: 400px;
  margin: 2rem auto;
  padding: 2rem;
  border: 1px solid #ddd;
  border-radius: 8px;
}
.login-page h1 {
  text-align: center;
  margin-bottom: 1.5rem;
}
</style>