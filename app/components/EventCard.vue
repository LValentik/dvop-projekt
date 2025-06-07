<template>
    <UCard class="flex flex-col justify-between eventcard">
        <template #header>
            <img
                :src="thumbnailUrl"
                alt="Event Image"
                class="w-full h-1/3 object-cover rounded-md"
            >
        </template>
        <h1 class="text-xl mb-2">{{ event.title }}</h1>
        <p><strong>Date:</strong> {{ formattedDate }}</p>
        <p><strong>Location:</strong> {{ event.location }}</p>

        <template #footer>
            <div class="flex justify-end">
                <NuxtLink :to="`/events/${event.slug}`">
                    <UButton color="primary">More...</UButton>
                </NuxtLink>
            </div>
        </template>
    </UCard>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
    event: {
        type: Object,
        required: true,
    }
})

const viteStrapiUrl = import.meta.env.VITE_STRAPI_URL

const thumbnailUrl = props.event.image?.formats?.medium?.url
    ? viteStrapiUrl + props.event.image.formats.medium.url
    : ''

const formattedDate = computed(() => {
  if (!props.event?.date) return ''
  return new Date(props.event.date).toLocaleString('cs-CZ', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
})
</script>

<style scoped>
.eventcard {
    background-color: #001e3b; /* Light gray background */
    border-radius: 0.5rem; /* Rounded corners */
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1); /* Subtle shadow */
}

</style>