<template>
  <client-only>
    <pre
      class="my-3"
      style="
        position: fixed;
        top: 10px;
        right: 10px;
        z-index: 9999;
        overflow-y: auto;
        background: rgba(0, 0, 0, 0.5);
        color: white;
        padding: 10px;
        border-radius: 5px;
        width: 225px;
        border: 1px dashed #cccccc5c;
        font-size: 0.75rem;
      "
      onclick="this.style.display='none'">
--
{{ stats }}
</pre
    >

    <div class="row">
      <search-filters ref="filters" @search="search('filters')"></search-filters>

      <div class="col-12">
        <search-results ref="results" :layout="f.show?.layout" />

        <!--
        *+---------------------------------
        *| Loading block
        *| We are getting there...
        *+--------------------------------- -->
        <!-- <div
        v-if="loading"
        class="empty"
        style="border: 1px dashed #cccccc73; border-radius: 4px">
        <v-progress-linear
          :location="null"
          bg-color="#92aed9"
          buffer-color="#6a3e0b"
          buffer-opacity="1"
          buffer-value="3"
          color="#12512a"
          height="12"
          max="9"
          min="0"
          model-value="2"
          rounded></v-progress-linear>
        <div class="ms-4 text-h6">3/9</div>
      </div> -->

        <!--#
        *+---------------------------------
        *| Empty block
        *| Message when there are no results
        *+--------------------------------- -->
        <div
          v-if="$app.ready && stats.results == 0 && !loading"
          class="empty p-0"
          style="border: 1px dashed #cccccc73; border-radius: 4px">
          <template v-if="isLibrary && stats.apps == 0">
            <p class="empty-title mb-3 font-serif" style="font-weight: 300">
              {{ $t('messages.libraryEmpty') }}
            </p>
            <p
              class="empty-subtitle text-secondary"
              v-html="$t('messages.libraryEmptySubtitle')"></p>
            <p class="empty-subtitle text-secondary" v-html="$t('messages.libraryEmptySubtitle')"></p>
            <div class="empty-action">
              <b-btn to="/import" class="me-3">
                <!-- <Icon>StepInto</Icon> -->
                {{ $t('buttons.importLibrary') }}
              </b-btn>

              <!-- <b-btn class="me-3" @click.stop="$mitt.emit('game:add')">
                Manually add a game
              </b-btn> -->

              <b-btn to="/games">{{ $t('buttons.browseAllGames') }}</b-btn>
            </div>
          </template>

          <div v-if="stats.apps > 0 && stats.results == 0" class="row w-100">
            <div
              class="col-6"
              style="display: flex; flex-direction: column; align-items: center">
              <img src="/img/transhumans/reflecting.png" max-width="80%" />
              <!-- <b-game
                :data="{ uuid: 'placeholder' }"
                tracking
                disabled
                style="max-width: 250px"></b-game> -->
            </div>
            <div
              class="col-4"
              style="
                display: flex;
                flex-direction: column;
                align-items: center;
                justify-content: center;
              ">
              <p class="empty-title mb-3 font-serif" style="font-weight: 300">
                {{ $t('messages.nothingFound') }}
                <template v-if="f.string && f.string.length">
                  <strong>"{{ f.string }}"</strong>
                </template>
                <template v-else>with your filters</template>
              </p>
              <p class="empty-subtitle text-secondary">
                {{ $t('messages.noGamesFound') }}
                <template v-if="isLibrary">
                  You can either
                  <strong>Import your steam games</strong>
                  or
                  <strong>repeat the search again with all games</strong>
                </template>
              </p>
              <p v-if="!isLibrary" class="empty-subtitle text-secondary">
                The results include all games in your library as well as all
                <a href="https://www.igdb.com" target="_blank">
                  <img
                    class="mx-2 text-muted"
                    width="25"
                    src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIzNCIgaGVpZ2h0PSIxNiIgdmlld0JveD0iMCAwIDM0IDE2IiBmaWxsPSJub25lIj4KICAgIDxwYXRoCiAgICAgICAgZD0iTTYuNzE2ODVlLTA1IDAuMDAwOTExNDY4QzExLjMzMzEgMC4wMDA2ODM4MjMgMjIuNjY2NSAwLjAwMjUwNDA5IDMzLjk5OTggMEMzNCA1LjMzMzI2IDM0LjAwMDIgMTAuNjY2NyAzMy45OTk1IDE2QzMxLjc5MzcgMTUuNjUyNCAyOS41Nzc5IDE1LjM2MTIgMjcuMzU0IDE1LjE0ODJDMTkuMzI5MSAxNC4zNjg1IDExLjIxMjIgMTQuNDk5MSAzLjIxNzc4IDE1LjUzNjdDMi4xNDI1NyAxNS42NzMxIDEuMDcxMDkgMTUuODM1OSA2LjcxNjg1ZS0wNSAxNS45OTkzQy0wLjAwMDE2NTUxIDEwLjY2NjUgMC4wMDAyOTk4NDcgNS4zMzM3MSA2LjcxNjg1ZS0wNSAwLjAwMDkxMTQ2OFpNMS4wMDA4MiAwLjk4MDIzOEMxLjAwMTI4IDUuNjA1NzUgMS4wMDA4MiAxMC4yMzE1IDEuMDAwODIgMTQuODU3QzExLjU4NDcgMTMuMjcyMSAyMi40MTU0IDEzLjI3MDggMzIuOTk5IDE0Ljg1NzVDMzIuOTk5NyAxMC4yMzE1IDMyLjk5OTIgNS42MDU1MiAzMi45OTkyIDAuOTc5NTU1QzIyLjMzMyAwLjk4MTE0OSAxMS42NjY4IDAuOTgwMDEgMS4wMDA4MiAwLjk4MDIzOFoiCiAgICAgICAgZmlsbD0id2hpdGUiCiAgICAvPgogICAgPHBhdGgKICAgICAgICBkPSJNOC4zMTkyNiA0LjYxOEM5LjAxMjg3IDMuOTU3MzcgOS45ODU5NCAzLjYwNTQ0IDEwLjk0OTcgMy42MjAyM0MxMS42MDc3IDMuNjE3OTYgMTIuMjc5NCAzLjcxODggMTIuODcxMiA0LjAxMjY5QzEzLjE3NjIgNC4xNjE1NyAxMy40NTQ5IDQuMzU2ODkgMTMuNzE1MSA0LjU3MTU2QzEzLjM4NzcgNC45NTgzMyAxMy4wNTc1IDUuMzQyNTkgMTIuNzMzOSA1LjczMjA5QzEyLjUzOTEgNS41ODkzNiAxMi4zNDk3IDUuNDM3MDYgMTIuMTM0MyA1LjMyNDE1QzExLjcwMTcgNS4wODUzNSAxMS4xOTIxIDUuMDAyNzIgMTAuNzAwNSA1LjAzNzc4QzEwLjA5ODggNS4wODgwOCA5LjU0ODI3IDUuNDQyOTggOS4yMjIyOCA1LjkzNDQ3QzguODAyMDcgNi41NTE4NCA4LjczNjY4IDcuMzY0MyA4Ljk5MTIzIDguMDU1NjZDOS4xNDUyNyA4LjQ3NTY2IDkuNDM5MzcgOC44NDgzMiA5LjgyNjc4IDkuMDg2MjFDMTAuMjEyMSA5LjMyODQyIDEwLjY4IDkuNDI1NjIgMTEuMTM0MiA5LjM5ODk5QzExLjYgOS4zODE0NiAxMi4wNzQyIDkuMjU3MTcgMTIuNDUzNSA4Ljk4NDY4QzEyLjQ0OTggOC42NTY4NyAxMi40NTM1IDguMzI4ODMgMTIuNDUxNiA4LjAwMTAyQzExLjkyOTcgOC4wMDIzOSAxMS40MDc2IDguMDAwNTcgMTAuODg1NyA4LjAwMTk0QzEwLjg4NDUgNy41NjIxMyAxMC44ODg1IDcuMTIyMzIgMTAuODgzOCA2LjY4MjczQzExLjkxMDQgNi42NzYzNiAxMi45MzcyIDYuNjg1MDEgMTMuOTYzOCA2LjY3ODQxQzEzLjk3MDEgNy42ODI1NSAxMy45NjMxIDguNjg2OTIgMTMuOTY3MyA5LjY5MTA2QzEzLjI2MDcgMTAuMjg3OSAxMi4zNjg4IDEwLjY3OTUgMTEuNDM3NCAxMC43Njg1QzEwLjUyODEgMTAuODY2MiA5LjU2OTQ0IDEwLjcwMjUgOC43OTQzOSAxMC4yMTE5QzguMTczMzcgOS44MjQ2OCA3LjY5MzU5IDkuMjMwNzYgNy40NDI1MyA4LjU1NDJDNy4xOTQ3MiA3Ljg4ODU3IDcuMTQ1ODYgNy4xNTUxIDcuMjg1IDYuNDYxMjRDNy40Mjk5NiA1Ljc2MzczIDcuNzkyNDcgNS4xMDk3MSA4LjMxOTI2IDQuNjE4WiIKICAgICAgICBmaWxsPSJ3aGl0ZSIKICAgIC8+CiAgICA8cGF0aAogICAgICAgIGQ9Ik0zLjc4NzYxIDMuNzM5NTJDNC4zMDgxMSAzLjc0MDY2IDQuODI4NjEgMy43Mzg2MSA1LjM0OTEyIDMuNzQwNjZDNS4zNDc5NSA2LjA1MDc5IDUuMzQ5MTIgOC4zNjExNiA1LjM0ODY1IDEwLjY3MTVDNC44MjgzOCAxMC42NzEzIDQuMzA4MTEgMTAuNjcwNiAzLjc4Nzg0IDEwLjY3MThDMy43ODc2MSA4LjM2MTE2IDMuNzg4MDggNi4wNTAzNCAzLjc4NzYxIDMuNzM5NTJaIgogICAgICAgIGZpbGw9IndoaXRlIgogICAgLz4KICAgIDxwYXRoCiAgICAgICAgZD0iTTE1Ljg2NDMgMy43Mzk3NUMxNi44MTY1IDMuNzM5OTcgMTcuNzY4NiAzLjczOTc1IDE4LjcyMDcgMy43Mzk5N0MxOS41ODYzIDMuNzQ5MzEgMjAuNDYyOCA0LjAxMjAxIDIxLjE0MDEgNC41NDkwMkMyMS43MzIzIDUuMDEyMjggMjIuMTUyMiA1LjY3ODYgMjIuMzEyMyA2LjQwMzY0QzIyLjUwNjEgNy4yNzkxNiAyMi4zNzk4IDguMjMzOTEgMjEuODk5MyA5LjAwNDAzQzIxLjUxOCA5LjYyNDgxIDIwLjkxOSAxMC4xMDgxIDIwLjIzNDUgMTAuMzc5MkMxOS43NTQ5IDEwLjU2OTggMTkuMjM3NyAxMC42Njk5IDE4LjcyIDEwLjY3MTNDMTcuNzY4MyAxMC42NzEzIDE2LjgxNjUgMTAuNjcwOCAxNS44NjQ2IDEwLjY3MTVDMTUuODY0NiA4LjM2MDkzIDE1Ljg2NSA2LjA1MDM0IDE1Ljg2NDMgMy43Mzk3NVpNMTcuNDMwMyA1LjExNjU0QzE3LjQyNzkgNi41MDkyNyAxNy40MzA1IDcuOTAyIDE3LjQyODkgOS4yOTQ5NkMxNy43Mjc2IDkuMjk0NSAxOC4wMjY2IDkuMjk0NzMgMTguMzI1NCA5LjI5NDczQzE4LjU2NiA5LjI5MjkxIDE4LjgwODYgOS4zMDU4OCAxOS4wNDY5IDkuMjYyNjNDMTkuNTEzOSA5LjE5MTYxIDE5Ljk2MzkgOC45NzE5MyAyMC4yNzI3IDguNjE3NDlDMjAuNTQyNiA4LjMxMzgxIDIwLjY5OTQgNy45MjQzMSAyMC43NDY2IDcuNTI3MDdDMjAuNzkwNiA3LjEyMzY4IDIwLjc1NTcgNi43MDU3MyAyMC41OTU4IDYuMzI4NzVDMjAuNDMxMyA1LjkyODA5IDIwLjEyMzcgNS41ODUyNiAxOS43MzUyIDUuMzc5N0MxOS4zODQzIDUuMTg4NDggMTguOTc5IDUuMTE2MzEgMTguNTgwNiA1LjExNjA5QzE4LjE5NzIgNS4xMTcgMTcuODEzNyA1LjExNjA4IDE3LjQzMDMgNS4xMTY1NFoiCiAgICAgICAgZmlsbD0id2hpdGUiCiAgICAvPgogICAgPHBhdGgKICAgICAgICBkPSJNMjQuMTgzOCAzLjc0NDA3QzI1LjE0NTkgMy43MzQwNiAyNi4xMDg5IDMuNzQyNzEgMjcuMDcxMyAzLjczOTc1QzI3LjMzNTYgMy43NDMzOSAyNy42MDA2IDMuNzI5MDUgMjcuODY0IDMuNzU1NjhDMjguMzMxOSAzLjc5ODI1IDI4LjgwNjEgMy45MzE4OCAyOS4xODA1IDQuMjE5MzlDMjkuNTA2IDQuNDY1MDIgMjkuNzM0NSA0LjgzMTc2IDI5Ljc5NzYgNS4yMzAzNkMyOS44NDMyIDUuNjA3MzQgMjkuODIyIDYuMDA4OTEgMjkuNjMyNCA2LjM0ODc4QzI5LjQ3MTEgNi42NTM2IDI5LjE5MzMgNi44Nzk4OCAyOC44OTQ2IDcuMDQ5N0MyOS4yOTI3IDcuMTk2MyAyOS42ODY0IDcuNDEyNTYgMjkuOTM0MiA3Ljc2MTMxQzMwLjE2NTIgOC4wODM0MyAzMC4yMzI3IDguNDkwNDYgMzAuMjEwMSA4Ljg3NjU1QzMwLjIwMDggOS4yNjkwMSAzMC4wNTQgOS42NjI2IDI5Ljc3OTIgOS45NTE5NEMyOS41MDE2IDEwLjI0ODYgMjkuMTIgMTAuNDMwOSAyOC43Mjg0IDEwLjUzNjhDMjguNDAxMSAxMC42MjMgMjguMDYyNSAxMC42Njc0IDI3LjcyMzcgMTAuNjcwNkMyNi41NDQ1IDEwLjY3MTUgMjUuMzY1MyAxMC42NzE4IDI0LjE4NjEgMTAuNjcwNkMyNC4xODQyIDguMzYxNjEgMjQuMTg4NiA2LjA1Mjg0IDI0LjE4MzggMy43NDQwN1pNMjUuNzA3OCA1LjA4MDM1QzI1LjcwOTQgNS41Njc1IDI1LjcwNjQgNi4wNTQ4OSAyNS43MDkyIDYuNTQyMjhDMjYuMjI1NyA2LjUzOTA5IDI2Ljc0MjMgNi41NDI5NiAyNy4yNTg4IDYuNTQwMjNDMjcuNDkzMSA2LjUyNzk0IDI3LjczNTggNi40OTUzOCAyNy45NDI5IDYuMzc5NzRDMjguMTA2NSA2LjI4OTE0IDI4LjIzMDcgNi4xMjY4MyAyOC4yNTUyIDUuOTQyNDRDMjguMjg5NiA1LjczNDYgMjguMjUzMSA1LjUwMTI2IDI4LjA5NzkgNS4zNDU3OEMyNy45MTEzIDUuMTU3NzQgMjcuNjMzIDUuMDk3NjUgMjcuMzc0OSA1LjA4MjYyQzI2LjgxOTMgNS4wNzgwNyAyNi4yNjM0IDUuMDgyNjIgMjUuNzA3OCA1LjA4MDM1Wk0yNS43MDc2IDcuODE1MjdDMjUuNzA4MyA4LjMyMTMyIDI1LjcwOSA4LjgyNzgzIDI1LjcwNzEgOS4zMzQxMUMyNi4yMDExIDkuMzM4NjcgMjYuNjk1MyA5LjMzNDU3IDI3LjE4OTUgOS4zMzU5M0MyNy40MzYxIDkuMzMyOTcgMjcuNjg1MyA5LjM0OTM2IDI3LjkyOTYgOS4zMDYzNEMyOC4xNDM3IDkuMjcyNjUgMjguMzY0NSA5LjE5Mzg4IDI4LjUwOSA5LjAyODYxQzI4LjY1NDcgOC44NjMxMSAyOC42ODUyIDguNjI3NzMgMjguNjQ4NiA4LjQxODk4QzI4LjYxOTUgOC4yNDE4NyAyOC41MDU4IDguMDgyNzUgMjguMzQ4NSA3Ljk5MTQ2QzI4LjEzNTEgNy44NjQ0NCAyNy44Nzk2IDcuODI0MzcgMjcuNjMzNCA3LjgxNjE4QzI2Ljk5MTUgNy44MTQzNiAyNi4zNDk1IDcuODE2MTggMjUuNzA3NiA3LjgxNTI3WiIKICAgICAgICBmaWxsPSJ3aGl0ZSIKICAgIC8+Cjwvc3ZnPgo="
                    alt="" />
                </a>
                games
              </p>

              <div v-if="isLibrary" class="empty-action mt-3">
                <b-btn to="/import" class="me-3">
                  <!-- <Icon>StepInto</Icon> -->
                  {{ $t('buttons.importLibrary') }}
                </b-btn>

                <b-btn @click="$refs.filters.changeSource('all')">
                  {{ $t('buttons.searchAllGames') }}
                </b-btn>
              </div>

              <template v-if="f.string && f.string.length">
                <div
                  class="hr-text font-serif w-100"
                  style="margin-top: 2.5rem !important; margin-bottom: 2.5rem !important">
                  ◈ Or you can ◈
                </div>
                <p class="empty-title mb-2 font-serif" style="font-weight: 300">
                  {{ $t('pages.createYourOwnGame') }}
                </p>
                <p class="empty-subtitle text-secondary">
                  {{ $t('pages.createYourOwnGameSubtitle') }}
                </p>
                <div class="empty-action mt-2">
                  <b-btn @click="createCustomGame">
                    <!-- <Icon>StepInto</Icon> -->
                    {{ $t('buttons.addGamePlaceholder', { title: f.string }) }}
                  </b-btn>
                </div>
              </template>
            </div>
          </div>
        </div>

        <search-pagination ref="pagination" @page-changed="search('pagination')" />
      </div>
    </div>
  </client-only>
</template>

<script>
/**
 * @file:    \components\search\interface.vue
 * @desc:    ...
 * -------------------------------------------
 * Created Date: 16th November 2023
 * Modified: 1st July 2025 - 04:36:01
 **/

export default {
  name: 'SearchInterface',
  props: {},

  data() {
    return {
      // presets: {
      //   'my-preset': {
      //     string: 'my preset',
      //   },
      // },

      ui: {
        ping: 0,
        dirty: false,
        ready: false,
      },
    }
  },

  computed: {
    ...mapStores(useDataStore, useGameStore, useRepositoryStore, useSearchStore),
    ...mapState(useSearchStore, ['f', 'stats', 'loading']),

    //+-------------------------------------------------
    // isLibrary()
    // Returns true when browsing library
    //+-------------------------------------------------
    isLibrary() {
      return this.f?.source == 'library'
    },
  },

  watch: {
    '$app.ready': function () {
      this.init()
    },
  },

  methods: {
    search(by) {
      this.$nextTick(() => {
        this.$refs.results.search(by)
      })
    },

    //+-------------------------------------------------
    // createCustomGame()
    //
    // -----
    // Created on Tue Oct 15 2024
    //+-------------------------------------------------
    createCustomGame() {
      const app = this.gameStore.create({
        name: this.f.string,
      })

      this.dataStore.process(app, 'add:new')
      this.$toast.success(app.name + ' has been added to your library')
    },

    //+-------------------------------------------------
    // mergeFilters()
    // Initializes this.f
    // With values from props and presets
    // -----
    // Created on Tue Nov 14 2023
    // Updated on Sun Jan 28 2024 - Added slug param
    // Updated on Sun Jul 14 2024 - Use slug for special filters
    // Updated on Thu Sep 19 2024 - In library, sort by playtime
    // Updated on Fri Oct 25 2024 - Dynamically attempt to find the genre
    //+-------------------------------------------------
    // TODO: Make this a function in searchService.handleFilters()
    // That should handle
    // - a default object,
    // - route path and query params,
    // - and a given json
    //+-------------------------------------------------
    // async mergeFilters() {
    //   const loaded = {}
    //   let slugged = false

    //   //Handle base sort options for library
    //   //+~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    //   if (this.$route.path.includes('library')) {
    //     loaded.source = 'library'
    //     loaded.sortBy = 'playtime'
    //     loaded.sortDir = 'desc'
    //   }

    //   // Handle slug for special library filters
    //   //+~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    //   this.slug = this.$route.params?.slug || null
    //   if (this.slug && typeof this.slug == 'object') this.slug = this.slug[0]

    //   if (this.slug) {
    //     if (['pinned', 'hidden', 'favorites'].includes(this.slug)) {
    //       loaded.source = 'library:' + this.slug
    //       slugged = true
    //     }

    //     // Dynamically add state as a filter
    //     // When the slug is set and found in the states array
    //     //+~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    //     if (!slugged && this.states.length) {
    //       const state = this.states.find((g) => g.slug == this.slug)
    //       if (state) {
    //         loaded.states = [state.id]
    //         slugged = true
    //       }
    //     }

    //     // Dynamically add genre as a filter
    //     // When the slug is set and found in the genres array
    //     //+~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    //     if (!slugged) {
    //       let genres = null

    //       if (!this.genres.length) genres = await this.repositoryStore.getGenres()
    //       else genres = this.genres

    //       const genre = genres.find((g) => g.slug == this.slug)
    //       if (genre) loaded.genres = [genre.id]
    //     }
    //   }

    //   this.f = {
    //     ...this.base,
    //     ...(this.preset ? this.presets[this.preset] : {}),
    //     ...this.filters,
    //     ...loaded,
    //   }
    // },

    //+-------------------------------------------------
    // getData()
    // Loads data from the repository
    // -----
    // Created on Tue Sep 24 2024
    //+-------------------------------------------------
    async getData() {
      if (this.$route.path !== '/games') return
      this.repositoryStore.topGames('popular')
    },

    //+-------------------------------------------------
    // init()
    // -----
    // Created on Tue Jan 30 2024
    //+-------------------------------------------------
    async init() {
      if (!this.$app.ready) return

      this.getData()
      this.ui.ready = true
    },
  },

  mounted() {
    this.init()
  },
}
</script>
