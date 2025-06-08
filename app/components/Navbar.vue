<template>
  <nav class="bg-white shadow mb-6">
    <div class="container mx-auto px-4 py-3 flex justify-between items-center">
      <span class="font-bold text-lg text-gray-950">CreativeHub</span>
      <div class="space-x-4">
        <NuxtLink to="/" class="text-gray-700 hover:text-blue-600">Home</NuxtLink>
        <NuxtLink to="/events" class="text-gray-700 hover:text-blue-600">Events</NuxtLink>
        <NuxtLink to="/contact" class="text-gray-700 hover:text-blue-600">Contact</NuxtLink>
        <template v-if="isLoggedIn">
          <UButton color="primary" class="text-white cursor-default" @click="$router.push('/account')">
            <span class="mr-2">👤</span>{{ username }}
          </UButton>
        </template>
        <template v-else>
          <UButton
            color="primary"
            class="text-white"
            @click="$router.push('/login')"
          >Login</UButton>
        </template>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const isLoggedIn = ref(false)
const username = ref('')

onMounted(async () => {
  // Check for JWT in cookie
  const jwt = document.cookie.split('; ').find(row => row.startsWith('jwt='))
  if (jwt) {
    isLoggedIn.value = true
    // Optionally fetch user profile from Strapi
    try {
      const token = jwt.split('=')[1]
      const res = await fetch('http://localhost:1337/api/users/me', {
        headers: { Authorization: `Bearer ${token}` }
      })
      if (res.ok) {
        const user = await res.json()
        username.value = user.username
      }
    } catch {
      // If error, treat as not logged in
      isLoggedIn.value = false
      username.value = ''
    }
  }
})
</script>