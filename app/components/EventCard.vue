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
        <p><strong>Date:</strong> {{ event.date }}</p>
        <p><strong>Location:</strong> {{ event.location }}</p>

        <template #footer>
            <div class="flex justify-end">
                <UButton color="primary" @click="$emit('details', event.slug)">More...</UButton>
            </div>
        </template>
    </UCard>
</template>

<script setup>
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
</script>

<style scoped>
.eventcard {
    background-color: #001e3b; /* Light gray background */
    border-radius: 0.5rem; /* Rounded corners */
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1); /* Subtle shadow */
}
</style>