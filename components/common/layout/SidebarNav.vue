<template>
  <div
    class="d-lg-block dropdown-menu bg-transparent"
    style="
      height: calc(100% - 220px);
      overflow-y: auto;
      overflow-x: hidden;
      border-radius: 4px;
    ">
    <!--
      *+---------------------------------
      *| Explore
      *+--------------------------------- -->
    <span class="dropdown-header">
      <span class="text-muted my-2">{{ $t('menu.explore') }}</span>
    </span>

    <!-- <div class="dropdown-item disabled text-white">
            <h6 class="m-0">Explore</h6>
          </div> -->

    <NuxtLink to="/games" class="dropdown-item">
      <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
        <Icon size="16">Cards</Icon>
      </span>
      <span class="nav-link-title">{{ $t('menu.allGames') }}</span>
    </NuxtLink>

    <NuxtLink to="/genres" class="dropdown-item">
      <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
        <Icon size="16">Triangle</Icon>
      </span>
      <span class="nav-link-title">{{ $t('menu.genres') }}</span>
    </NuxtLink>

    <template v-if="$app.wip">
      <div class="dropdown-divider"></div>

      <span class="dropdown-header">
        <span class="text-muted my-2">{{ $t('menu.community') }}</span>
      </span>

      <NuxtLink to="/community" class="dropdown-item">
        <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
          <Icon size="16">Components</Icon>
        </span>
        <span class="nav-link-title">{{ $t('menu.community') }}</span>
      </NuxtLink>
    </template>

    <NuxtLink v-if="$app.dev" to="/dashboard" class="dropdown-item">
      <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
        <Icon size="16">Components</Icon>
      </span>
      <span class="nav-link-title">{{ $t('menu.dashboard') }}</span>
    </NuxtLink>
    <!--

          <NuxtLink to="/journal" class="dropdown-item">
            <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
              <Icon size="16">Notebook</Icon>
            </span>
            <span class="nav-link-title">Journal</span>
            <small class="ms-auto text-secondary">
              {{ $moment().format('DD/MM') }}
            </small>
          </NuxtLink> -->

    <!-- <div class="dropdown-divider"></div>
          <span class="dropdown-header control-hover" style="pointer-events: all">
            <Icon
              style="float: right; outline: none; transform: translateX(4px)"
              class="ms-auto text-secondary show-hover cursor-pointer"
              size="16"
              v-tippy="$t('tooltips.createList')"
              @click.prevent="$mitt.emit('list:create')">
              SquareRoundedPlus
            </Icon>
            <span class="text-muted my-2">Your Lists</span>
          </span> -->

    <div class="dropdown-divider"></div>

    <span class="dropdown-header">
      <span class="text-muted my-2">{{ $t('labels.yourGames') }}</span>
    </span>

    <NuxtLink to="/library" class="dropdown-item control-hover">
      <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
        <Icon size="16">LayoutDashboard</Icon>
      </span>
      <span class="nav-link-title">{{ $t('menu.library') }}</span>

      <small class="ms-auto text-secondary hide-hover">
        {{ format.num($app.count.library) }}
      </small>

      <Icon
        v-tippy="$t('tooltips.configure')"
        style="outline: none; transform: translateX(4px)"
        class="ms-auto me-1 text-secondary show-hover cursor-pointer"
        size="15"
        @click.prevent="goTo('/account/preferences')">
        Settings2
      </Icon>
    </NuxtLink>

    <NuxtLink to="/account/lists" class="dropdown-item control-hover">
      <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
        <Icon size="16">Mist</Icon>
      </span>

      <span class="nav-link-title">{{ $t('menu.yourLists') }}</span>

      <small class="ms-auto text-secondary hide-hover">
        {{ format.num($app.count.lists) }}
      </small>

      <Icon
        v-tippy="$t('tooltips.createList')"
        style="float: right; outline: none; transform: translateX(4px)"
        class="ms-auto me-1 text-secondary show-hover cursor-pointer"
        size="15"
        @click.prevent="$mitt.emit('list:create')">
        SquareRoundedPlus
      </Icon>

      <!-- <small class="ms-auto text-secondary hide-hover">
              {{ format.num($app.count.library) }}
            </small> -->
    </NuxtLink>

    <NuxtLink v-if="$auth.menu.favorites" to="/library/favorites" class="dropdown-item">
      <div class="content d-flex align-items-center w-100">
        <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
          <Icon size="16">Heart</Icon>
        </span>
        <span class="nav-link-title me-4">{{ $t('menu.favorites') }}</span>
        <small v-if="$app.count.fav > 0" class="ms-auto text-secondary">
          {{ format.num($app.count.fav) }}
        </small>
      </div>
    </NuxtLink>

    <NuxtLink v-if="$auth.menu.pinned" to="/library/pinned" class="dropdown-item">
      <div class="content d-flex align-items-center w-100">
        <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
          <Icon size="16">Bookmark</Icon>
        </span>

        <span class="nav-link-title me-4">{{ $t('menu.pinned') }}</span>
        <small v-if="$app.count.pinned > 0" class="ms-auto text-secondary">
          {{ format.num($app.count.pinned) }}
        </small>
      </div>
    </NuxtLink>

    <NuxtLink
      v-for="(state, i) in pinnedStates"
      :key="'state' + i"
      :to="'/library/' + state.slug"
      class="dropdown-item ps-3">
      <div class="content d-flex align-items-center w-100 ps-1">
        <span
          class="status-dot me-2"
          style="transform: translateX(-4px)"
          :style="{ 'background-color': state.color || '' }"></span>

        <span class="ps-1 me-4">
          {{ state.name }}
        </span>

        <small v-if="$app.count.states[state.key] > 0" class="ms-auto text-secondary">
          {{ format.num($app.count.states[state.key]) }}
        </small>

        <!-- <span
                v-if="stateStore.count(state.id) > 0"
                class="badge bg-purple-lt ms-auto">
                {{ format.num(stateStore.count(state.id)) }}
              </span> -->
        <!-- <tippy
                class="text-muted ms-auto ms-1 cursor-help"
                :content="state.description">
                <Icon size="18" stroke="1">HelpCircleFilled</Icon>
              </tippy> -->
      </div>
    </NuxtLink>

    <NuxtLink to="/import" class="dropdown-item">
      <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
        <Icon size="16">Refresh</Icon>
      </span>
      <span class="nav-link-title">
        {{ $t('menu.librarySync') }}
        <!-- <Icon size="10" width="1" class="ms-1">Refresh</Icon> -->
      </span>
    </NuxtLink>
  </div>
</template>

<script>
/**
 * @file:    \components\common\layout\SidebarNav.vue
 * @desc:    ...
 * ----------------------------------------------
 * Created Date: 26th March 2025
 * Modified: Thu 29 May 2025 - 17:26:09
 **/

export default {
  name: 'SidebarNav',

  computed: {
    ...mapState(useStateStore, ['states', 'pinnedStates', 'unPinnedStates']),
  },

  methods: {
    goTo(path) {
      this.$router.push(path)
    },
  },
}
</script>
