<script setup>
import { ref, onMounted, computed } from 'vue'

const events = ref([])
const viteStrapiUrl = import.meta.env.VITE_STRAPI_URL
const router = useRouter()
async function fetchEvents() {
  const res = await fetch(`${viteStrapiUrl}/api/events?populate=image`)
  if (res.ok) {
    const json = await res.json()
    events.value = json.data
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

const displayedEvents = computed(() => events.value.slice(0, 3))
</script>
<template>
  <div class="container mx-auto p-4">
    <div class="flex justify-end mb-4">
      <UButton
        color="primary"
        class="text-gray-900"
        @click="$router.push('/events')">More Events
    </UButton>
    </div>
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
      <template v-if="displayedEvents.length">
        <EventCard
          v-for="event in displayedEvents"
          :key="event.id"
          :event="event"
          @details="viewDetails(event.slug)"
        />
      </template>
      <div v-else class="col-span-full text-center">
        <p class="text-gray-500">No events available at the moment.</p>
      </div>
    </div>
  </div>
</template>
