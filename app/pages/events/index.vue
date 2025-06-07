<script setup>
import { ref, onMounted } from 'vue'
const events = ref([])
const viteStrapiUrl = import.meta.env.VITE_STRAPI_URL

async function fetchEvents() {
  const res = await fetch(`${viteStrapiUrl}/api/events?populate=image`)
  if (res.ok) {
    const json = await res.json()
    events.value = json.data // Use the "data" array from the response
    console.log('Events fetched:', events.value)
  } else {
    console.error('Failed to fetch events:', res.statusText)
  }
}

function viewDetails(slug) {
  // Implement navigation or modal logic here
  console.log('View details for', slug)
}

onMounted(fetchEvents)
</script>
<template>
    <div class="container mx-auto p-4">
        <h1 class="text-2xl font-bold mb-4">Upcoming Events</h1>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
            <EventCard
                v-if="events.length"
                v-for="event in events"
                :key="event.slug"
                :event="event"
                @details="viewDetails(event.slug)"
            />
            <div v-else class="col-span-full text-center">
                <p class="text-gray-500">No events available at the moment.</p>
            </div>
        </div>
    </div>
</template>
