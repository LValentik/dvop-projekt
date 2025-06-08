<script setup>
import { useRoute, useRouter } from '#app'
import { onMounted } from 'vue'

const route = useRoute()
const router = useRouter()

onMounted(() => {
  const token = route.query.access_token
  if (token) {
    document.cookie = `jwt=${token}; path=/; max-age=${60 * 60 * 24 * 7}`
    router.push('/') // Redirect to homepage or dashboard
  } else {
    router.push('/login')
  }
})
</script>

<template>
  <div>Logging you in...</div>
</template>