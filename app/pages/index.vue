<template>
  <Navbar />
  <div class="flex flex-col items-center justify-center min-h-screen bg-gray-100 text-gray-950">
    <h1 class="text-4xl font-bold mb-4">Welcome to CreativeHub!</h1>
    <p class="text-lg text-gray-700">
      Discover and join creative events in your community.
    </p>
  </div>
</template>

<script setup>
import Navbar from '~/components/Navbar.vue'
const route = useRouter()
onMounted(() => {
  route.push('/test-page') // Redirect to a test page on mount
})
</script>