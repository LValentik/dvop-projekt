<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from '#app'

const user = ref(null)
const router = useRouter()

onMounted(async () => {
  // Get JWT from cookie
  const jwt = document.cookie.split('; ').find(row => row.startsWith('jwt='))
  if (!jwt) {
    router.push('/login')
    return
  }
  const token = jwt.split('=')[1]
  try {
    const res = await fetch('http://localhost:1337/api/users/me', {
      headers: { Authorization: `Bearer ${token}` }
    })
    if (res.ok) {
      user.value = await res.json()
    } else {
      router.push('/login')
    }
  } catch {
    router.push('/login')
  }
})

function logout() {
  // Remove the JWT cookie by setting it expired
  document.cookie = 'jwt=; path=/; max-age=0'
  router.push('/login')
}
</script>

<template>
  <div v-if="user" class="w-5xl mx-auto mt-10 p-6 bg-white rounded shadow text-gray-900">
    <h1 class="text-2xl font-bold mb-4 gray">Account Info</h1>
    <p><strong>Username:</strong> {{ user.username }}</p>
    <p><strong>Email:</strong> {{ user.email }}</p>
    <UButton color="red" class="mt-6" @click="logout">Logout</UButton>
  </div>
</template>