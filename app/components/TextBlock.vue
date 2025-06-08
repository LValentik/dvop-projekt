<template>
  <div
    :class="[
      'w-[60vw]',
      paddingTop ? 'pt-12' : '',
      paddingBottom ? 'pb-12' : '',
      alignmentClass,
      sizeClass
    ]"
  >
    <h2 v-if="heading" class="mb-4 font-bold" :class="headingSizeClass">
      {{ heading }}
    </h2>
    <div v-if="content" class="prose max-w-none pb-1" v-html="richTextHtml"></div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  heading: String,
  content: [String, Array, Object], // Rich text (Blocks) from Strapi
  paddingTop: Boolean,
  paddingBottom: Boolean,
  alignment: {
    type: String,
    default: 'left' // 'center', 'left', 'right'
  },
  size: {
    type: String,
    default: 'default' // 'default', 'small', 'medium', 'large', 'x-large'
  }
})

// Alignment classes
const alignmentClass = computed(() => {
  switch (props.alignment) {
    case 'center': return 'text-center'
    case 'right': return 'text-right'
    default: return 'text-left'
  }
})

// Size classes
const sizeClass = computed(() => {
  switch (props.size) {
    case 'small': return 'mx-auto text-base'
    case 'medium': return 'mx-auto text-lg'
    case 'large': return 'mx-auto text-xl'
    case 'x-large': return 'mx-auto text-2xl'
    default: return 'mx-auto text-base'
  }
})

// Heading size
const headingSizeClass = computed(() => {
  switch (props.size) {
    case 'small': return 'text-xl'
    case 'medium': return 'text-2xl'
    case 'large': return 'text-3xl'
    case 'x-large': return 'text-4xl'
    default: return 'text-2xl'
  }
})

// Render rich text (basic, for Strapi blocks)
const richTextHtml = computed(() => {
  // If content is a string, return as is
  if (typeof props.content === 'string') return props.content
  // If content is Strapi blocks, join all children text with spacing
  if (Array.isArray(props.content)) {
    return props.content
      .map(block =>
        `<p style="margin-bottom:1em;">${(block.children || []).map(child => child.text).join('')}</p>`
      )
      .join('')
  }
  return ''
})
</script>