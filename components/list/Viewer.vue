<template>
  <div class="row gx-1 mb-1 align-items-center">
    <div v-if="false && !ui.showSearch" class="col-auto">
      <!-- <div class="btn" size="small" style="background: rgb(30 31 41 / 20%)">
        <Icon size="16" class="text-secondary mx-1" style="min-width: 1em">Search</Icon>
      </div> -->
      <v-btn
        size="small"
        variant="tonal"
        color="secondary"
        style="min-width: 20px; width: 40px; height: 30px; padding: 0"
        mstyle="
          background: rgb(30 31 41 / 20%);
          outline: #38394b solid 1px;
          min-width: 20px;
          width: 50px;
          height: 32px;
          padding: 0;
        "
        @click="ui.showSearch = !ui.showSearch">
        <Icon size="14" width="2" class="text-secondary">Search</Icon>
      </v-btn>
    </div>

    <div v-if="ui.showSearch" class="col-11">
      <v-text-field
        v-model="f.string"
        :placeholder="$t('placeholders.searchByName')"
        clearable
        density="comfortable"
        @update:model-value="search">
        <template #prepend-inner>
          <Icon size="16" class="text-secondary mx-1" style="min-width: 1em">Search</Icon>
        </template>
      </v-text-field>
    </div>
    <div class="col-auto">
      <v-btn id="⚓sortby" variant="text" size="small" color="blue-grey-lighten-1">
        {{ $t('labels.sortingBy') }}
        <strong class="ps-1">
          {{ sortLabel[f.sortBy] ?? '...' }}
          {{ !f.sortBy || !f.sortDir ? '' : f.sortDir == 'asc' ? '(Asc)' : '(Desc)' }}
        </strong>

        <Icon
          class="text-muted"
          size="14"
          width="2"
          style="transform: translate(5px, 1px)">
          ChevronDown
        </Icon>

        <b-tippy-sheety to="#⚓sortby" :autoclose="150" trigger="click">
          <search-sort-menu :f="f" :with-user="true" @sort="sortBy" />
        </b-tippy-sheety>
      </v-btn>
    </div>

    <div class="col-auto ms-auto">
      <v-btn
        v-tippy="$t('tooltips.showList')"
        icon
        size="small"
        variant="text"
        color="secondary"
        @click="f.show.layout = 'list'">
        <Icon size="16" width="1.5" class="text-secondary">ListDetails</Icon>
      </v-btn>

      <v-btn
        v-tippy="$t('tooltips.showGrid')"
        icon
        size="small"
        variant="text"
        color="secondary"
        @click="f.show.layout = 'grid'">
        <Icon size="16" width="1.5" class="text-secondary">LayoutGrid</Icon>
      </v-btn>
    </div>
  </div>

  <search-results ref="results" :disabled="false" :source="listGames" />

  <!--
    *+---------------------------------
    *| Empty block
    *| Message when there are no results
    *+--------------------------------- -->
  <div
    v-if="list.games.length == 0"
    class="empty"
    style="border: 1px dashed #cccccc73; border-radius: 4px; height: auto; padding: 4rem">
    <p class="empty-title mb-3 font-serif" style="font-weight: 300">This list is empty</p>
    <p class="empty-subtitle text-secondary">
      There is nothing here. To start, try adding some games to it.
      <br />
      You can add most games, even if it's not in your library.
    </p>
  </div>
</template>

<script>
/**
 * @file:    \components\list\viewer.vue
 * @desc:    ...
 * ----------------------------------------------
 * Created Date: 8th October 2024
 * Modified: Fri 31 January 2025 - 13:05:35
 **/

export default {
  name: 'ListViewer',

  data: () => ({
    // f: {
    //   string: '',
    //   sortBy: 'user',
    //   sortDir: null,
    //   show: {
    //     page: 1,
    //     perPage: 42,

    //     layout: 'list', //'grid',
    //   },
    // },

    ui: {
      dice: 4,
      showSearch: false,
    },
  }),

  computed: {
    ...mapStores(useListStore, useDataStore),
    ...mapState(useListStore, ['list']),
    ...mapState(useSearchStore, ['f', 'stats', 'loading', 'time']),

    listGames() {
      const games = []
      this.list.games.forEach((item) => {
        const app = this.dataStore.get(item.uuid)
        if (app) games.push(app)
      })

      return games
    },

    sortLabel() {
      return {
        name: 'Name',
        rand: 'Random',
        user: 'User position',
        hltb: 'How long to beat',
        score: 'Score',
        playtime: 'Your playtime',
        released: 'Release date',
      }
    },
  },

  methods: {
    //+-------------------------------------------------
    // sortBy()
    // Applies sortBy to the filters
    // -----
    // Created on Sun Mar 17 2024
    //+-------------------------------------------------
    // sortBy(sort) {
    //   if (sort.toggle && this.f.sortBy == sort.by) {
    //     this.f.sortDir = this.f.sortDir == 'asc' ? 'desc' : 'asc'
    //   } else {
    //     this.f.sortBy = sort.by
    //     this.f.sortDir = sort.dir
    //   }
    //   this.search('sort')
    //   // this.$refs.tippySort.hide()
    //   // this.notify()
    // },
    //+-------------------------------------------------
    // search()
    // Performs a search on the results component
    // -----
    // Created on Tue Oct 29 2024
    //+-------------------------------------------------
    // search(by) {
    //   this.$nextTick(() => {
    //     // this.searchStore.loading = true
    //     // this.searchStore.setTime('start')
    //     this.$refs.results.search(by)
    //   })
    // },
  },
}
</script>
