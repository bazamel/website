<template>
  <div class="kitsu-page software-integrations integrations">
    <SolutionHeaderBlock :page-key="page.slug" :header="page.meta.header" />

    <section class="section mt8 pt0">
      <template
        v-for="(group, gIndex) in page.meta.integrations.groups"
        :key="group.title"
      >
        <div
          class="section-subtitle has-text-centered"
          :class="{ mt4: gIndex > 0 }"
        >
          {{ group.subtitle }}
        </div>
        <h2 class="section-title has-text-centered">
          {{ group.title }}
        </h2>

        <div
          v-for="(row, rIndex) in group.rows"
          :key="rIndex"
          class="flexrow mt2"
          :class="{ mb4: rIndex === group.rows.length - 1 }"
        >
          <a
            v-for="item in row"
            :key="item.alt"
            :href="item.href"
            class="software flexrow-item"
            :class="item.linkClass"
          >
            <NuxtImg :src="item.image" :class="item.imageClass" :alt="item.alt" />
            <span v-if="item.community" class="made-by-community">
              {{ page.meta.communityContribution }}
            </span>
          </a>
        </div>
      </template>
    </section>

    <Trial />
  </div>
</template>

<script setup>
const { locale } = useI18n()
const slug = ref('software-integrations')

const { pageQuery } = usePage(locale, slug)

const { data: page } = await useAsyncData(
  `${slug.value}-${locale.value}`,
  pageQuery,
  { watch: [slug, locale] }
)

useSEO({
  title: 'CGWire | Kitsu / ' + page.value.meta.header.tagline,
  description: page.value.meta.header.explanation,
  imagePath: 'software-integrations.png'
})
</script>