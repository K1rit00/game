<template>
  <div class="p-2">
    <div class="empty" style="border: 1px dashed #cccccc73; border-radius: 4px">
      <template v-if="preset == null">
        <p class="empty-title">{{ $t('messages.libraryEmpty') }}</p>
        <p class="empty-subtitle text-secondary" v-html="$t('messages.libraryEmptySubtitle')"></p>
        <div class="empty-action">
          <b-btn to="/import" color="primary" size="sm" class="me-3">
            <!-- <Icon>StepInto</Icon> -->
            {{ $t('buttons.importLibrary') }}
          </b-btn>

          <b-btn to="/import" color="primary" size="sm" class="me-3">
            <!-- <Icon>StepInto</Icon> -->
            {{ $t('buttons.addGameManual') }}
          </b-btn>

          <b-btn to="/games" variant="ghost" size="sm" color="secondary">
            {{ $t('buttons.browseAllGames') }}
          </b-btn>
        </div>
      </template>

      <template v-if="preset == 'filtering'">
        <p class="empty-title">{{ $t('messages.nothingFound') }}</p>
        <p class="empty-subtitle text-secondary" v-html="$t('messages.nothingFoundSubtitle')"></p>
      </template>
    </div>
  </div>
</template>

<script>
/**
 * @file:    \components\search\empty.vue
 * @desc:    ...
 * -------------------------------------------
 * Created Date: 15th March 2024
 * Modified: Thu 19 December 2024 - 10:00:07
 **/

export default {
  name: 'SearchEmpty',
  props: {
    preset: {
      type: [String, Boolean],
      default: null,
    },

    title: {
      type: String,
      default: () => useI18n().t('messages.libraryEmpty'),
    },
  },

  data() {
    return {
      ui: {},
    }
  },

  methods: {},

  mounted() {},
}
</script>
