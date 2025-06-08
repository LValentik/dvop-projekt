<script setup>
const props = defineProps({
  image: {
    type: Object, // Strapi media object
    required: true
  },
  caption: {
    type: String,
    default: ''
  },
  size: {
    type: String,
    default: 'medium' // e.g. 'small', 'medium', 'large'
  }
})

const sizeClass = {
  small: 'max-w-xs',
  medium: 'max-w-md',
  default: 'max-w-3xl'
}
onMounted(() => {
  if (!props.image || !props.image.url) {
    console.warn('ImageBlock: No image URL provided.')
  }
})
</script>

<template>
  <figure :class="['flex flex-col items-center my-6', sizeClass[size] || sizeClass.medium]">
    <img
      v-if="image && image.url"
      :src="image.url.startsWith('http') ? image.url : `${image.url}`"
      :alt="caption"
      class="rounded shadow"
    />
    <figcaption v-if="caption" class="mt-2 text-gray-500 text-center text-sm">
      {{ caption }}
    </figcaption>
  </figure>
</template>