<template>
    <UCard>
        <template #header>
            <img
                :src="thumbnailUrl"
                alt="Event Image"
                class="w-full h-48 object-cover rounded-t-lg"
            >
        </template>
        <h3>{{ event.title }}</h3>
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

const thumbnailUrl = props.event.image?.formats?.thumbnail?.url
    ? viteStrapiUrl + props.event.image.formats.thumbnail.url
    : ''
</script>

<style scoped>

</style>