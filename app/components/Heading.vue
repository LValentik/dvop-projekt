<template>
    <component :is="tag" :class="headingClass">
        {{ text }}
    </component>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
    text: {
        type: String,
        required: true
    },
    size: {
        type: String,
        default: 'medium',
        validator: value => ['small', 'medium', 'large'].includes(value)
    }
})

const tag = computed(() => {
    switch (props.size) {
        case 'large': return 'h1'
        case 'medium': return 'h2'
        case 'small': return 'h3'
        default: return 'h2'
    }
})

const headingClass = computed(() => {
    return {
        'heading-small': props.size === 'small',
        'heading-medium': props.size === 'medium',
        'heading-large': props.size === 'large'
    }
})
</script>

<style scoped>
.heading-small {
    font-size: 1.25rem;
    font-weight: 600;
}
.heading-medium {
    font-size: 1.75rem;
    font-weight: 700;
}
.heading-large {
    font-size: 2.5rem;
    font-weight: 800;
}
</style>