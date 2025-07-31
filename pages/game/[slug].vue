<template>
  <div class="page-body">
    <div class="container">
      <template v-if="pending">
        <div>{{ $t('messages.loading') }}</div>
      </template>

      <template v-else-if="error">
        <h1>{{ $t('messages.dataNotFound') }}</h1>
        <p>{{ $t('messages.dataNotFoundSubtitle') }}</p>
      </template>

      <template v-else-if="app">
        <div id="game-folio">
          <game-folio :game="app" />
        </div>
      </template>
    </div>
  </div>
</template>

<script setup>
/**
 * @file:    \pages\game\[slug].vue
 * @desc:    ...
 * ----------------------------------------------
 * Created Date: 18th December 2023
 * Modified: Thu 23 January 2025 - 17:07:05
 **/

const route = useRoute()
const slug = computed(() => route.params.slug)
const $data = useDataStore()

const { data, pending, error } = await useFetch(
  () => `https://api.backlog.rip/get/${slug.value}.json`
)

const app = dataService.prepareToData({ ...unref(data) })

watchEffect(() => {
  console.log({
    k: '🔸',
    // data: unref(data),
    // app: unref(app),
    // pending: unref(pending),
    // error: unref(error),

    route: route.params,
    data: data.id,
    app: app.id,
  })
})

useHead(() => ({
  title: app.name ?? $t('messages.dataNotFound'),
  meta: [
    {
      name: 'description',
      content: data.value?.description ?? $t('messages.dataNotFound'),
    },
    {
      property: 'og:title',
      content: data.value?.title ?? $t('messages.dataNotFound'),
    },
    {
      property: 'og:description',
      content: data.value?.description ?? $t('messages.dataNotFound'),
    },
  ],
}))
</script>
