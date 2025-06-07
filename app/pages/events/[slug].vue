<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRoute } from 'vue-router'
import Navbar from '~/components/Navbar.vue'

const route = useRoute()
const event = ref(null)
const viteStrapiUrl = import.meta.env.VITE_STRAPI_URL

async function fetchEvent() {
  const res = await fetch(`${viteStrapiUrl}/api/events?filters[slug][$eq]=${route.params.slug}&populate=image`)
  if (res.ok) {
    const json = await res.json()
    event.value = json.data[0] // Get the first matching event
  }
}

onMounted(fetchEvent)

const formattedDate = computed(() => {
  if (!event.value?.date) return ''
  return new Date(event.value.date).toLocaleString('cs-CZ', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
})
</script>

<template>
  <Navbar />
  <div class="container mx-auto p-4" v-if="event">
    <img
      v-if="event.image?.formats?.medium?.url"
      :src="viteStrapiUrl + event.image.formats.medium.url"
      class="mb-4 rounded w-max h-64 object-cover"
      alt="Event Image"
    />
    <h1 class="text-3xl font-bold mb-4">{{ event.title }}</h1>
    <div class="mb-2">
      <p
        v-for="(block, idx) in event.description"
        :key="idx"
        class="text-md mb-2"
      >
        {{ block.children.map(child => child.text).join('') }}
      </p>
    </div>
    <p class="mt-6"><strong>Date:</strong> {{ formattedDate }}</p>
    <p><strong>Location:</strong> {{ event.location }}</p>
  </div>
  <div v-else class="text-center text-gray-500">Loading...</div>
</template>