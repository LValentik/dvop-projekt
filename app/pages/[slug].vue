<template>
  <Navbar />
  <div class="flex flex-col items-center min-h-screen">
    <div v-if="pageData" class="w-[60vw]">
      <template v-for="(block, idx) in pageData.content" :key="idx">
        <TextBlock
          v-if="block.__component === 'text.text-block'"
          :heading="block.Heading"
          :content="block.Content"
          :padding-top="block.paddingTop"
          :padding-bottom="block.paddingBottom"
          :alignment="block.Allignment"
          :size="block.size"
        />
        <ImageBlock
          v-else-if="block.__component === 'image.image-block'"
          :image="block.Image"
          :caption="block.Caption"
          :size="block.size"
        />
        <Heading
          v-else-if="block.__component === 'text.heading'"
          :text="block.text"
          :size="block.size"
          class="mb-8"
        />
        <EventList
          v-else-if="block.__component === 'list.event-event-list'"
          :title="block.Title"
          :description="block.Description"
          :filterBy="block.filterBy"
        />
        <!-- Add more component types here as you create them -->
      </template>
    </div>
    <div v-else class="text-center py-10 text-gray-500">
      Loading...
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute } from '#app'
import TextBlock from '~/components/TextBlock.vue'
import Navbar from '~/components/Navbar.vue'
import Heading from '~/components/Heading.vue'
import EventList from '~/components/EventList.vue'
import ImageBlock from '~/components/ImageBlock.vue'

const route = useRoute()
const pageData = ref(null)
const formFields = ref({})
const viteStrapiUrl = import.meta.env.VITE_STRAPI_URL

onMounted(async () => {
  const res = await fetch(`${viteStrapiUrl}/api/pages?filters[slug][$eq]=${route.params.slug}&populate[content][on][image.image-block][populate]=Image`)
  if (res.ok) {
    const json = await res.json()
    pageData.value = json.data[0] || null
    // Fetch full form details for each form block
    if (pageData.value?.content) {
      for (const block of pageData.value.content) {
        if (block.__component === 'form.form' && block.id) {
          const formRes = await fetch(`${viteStrapiUrl}/api/forms?filters[id][$eq]=${block.id}&populate=Field`)
          if (formRes.ok) {
            const formJson = await formRes.json()
            formFields.value[block.id] = formJson.data?.[0]?.attributes || {}
          }
        }
      }
    }
  }
})
</script>