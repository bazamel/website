<template>
  <ul class="customers">
    <CustomerLogoBlock
      v-for="studio in orderedStudios"
      :key="studio.meta.elementKey"
      :link="studio.meta.link"
      :element-key="studio.meta.elementKey"
      :name="studio.meta.name"
    />
  </ul>
</template>

<script setup>
const { locale } = useI18n()
const type = ref('vfx')

const { buildStudiosQuery } = useStudios(locale, type)

const { data: studios } = await useAsyncData(
  () => `studios-${type.value}-${locale.value}`,
  buildStudiosQuery,
  { watch: [locale, type] }
)

// Reuse the curated ranking from the VFX audience page: listed studios
// first, in that order, the rest in their natural order.
const { data: audience } = await useAsyncData(
  () => `audience-order-vfx-${locale.value}`,
  () =>
    queryCollection('jsonPages')
      .where('lang', '=', locale.value)
      .where('pageType', '=', 'audiences')
      .where('slug', '=', 'vfx')
      .first(),
  { watch: [locale] }
)

const orderedStudios = computed(() => {
  const order = audience.value?.meta?.studios || []
  const rank = studio => {
    const index = order.indexOf(studio.meta.elementKey)
    return index === -1 ? order.length : index
  }
  return [...(studios.value || [])].sort((a, b) => rank(a) - rank(b))
})
</script>
