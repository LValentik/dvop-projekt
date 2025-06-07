<template>
  <nav class="bg-white shadow mb-6">
    <div class="container mx-auto px-4 py-3 flex justify-between items-center">
      <span class="font-bold text-lg">My NuxtUI App</span>
      <div class="space-x-4">
        <NuxtLink to="/" class="text-gray-700 hover:text-blue-600">Home</NuxtLink>
        <NuxtLink to="/events" class="text-gray-700 hover:text-blue-600">Events</NuxtLink>
        <NuxtLink to="/contact" class="text-gray-700 hover:text-blue-600">Contact</NuxtLink>
      </div>
    </div>
  </nav>
</template>

<script setup>
// No script needed for this navbar
</script>