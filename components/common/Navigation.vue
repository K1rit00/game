<template>
  <div class="navbar-expand-md">
    <div id="navbar-menu" class="collapse navbar-collapse" :class="{ show: showMobMenu }">
      <div class="navbar navbar-light p-0">
        <div class="container-xl" style="padding-left: 60px">
          <ul class="navbar-nav">
            <li
              v-if="false"
              v-tippy="{ content: $t('tooltips.showMenu'), placement: 'bottom' }"
              class="nav-item cursor-pointer"
              @click="$app.toggleSidebar()">
              <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
                <Icon>LayoutSidebarInactive</Icon>
              </span>
            </li>

            <li v-if="false" style="border-right: 1px dashed #ccc; margin: 10px"></li>

            <li class="nav-item dropdown">
              <div class="nav-link dropdown-toggle cursor-pointer ps-0">
                <!-- <span class="nav-link-icon d-md-none d-lg-inline-block">
                  <Icon>ChartBubble</Icon>
                </span> -->
                <span class="nav-link-title pe-2">{{ $t('menu.explore') }}</span>
              </div>
              <b-dropdown>
                <NuxtLink to="/games" class="dropdown-item">
                  <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
                    <Icon>Cards</Icon>
                  </span>
                  <span class="nav-link-title">{{ $t('menu.allGames') }}</span>
                </NuxtLink>

                <NuxtLink to="/genres" class="dropdown-item">
                  <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
                    <Icon>Triangles</Icon>
                  </span>
                  <span class="nav-link-title">{{ $t('menu.genres') }}</span>
                </NuxtLink>
              </b-dropdown>

              <!-- <b-menu :arrow="false">
              </b-menu> -->
            </li>

            <!-- <li class="nav-item">
              <NuxtLink to="/games" class="nav-link">
                <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
                  <Icon>Cards</Icon>
                </span>
                <span class="nav-link-title">
                  Browse games
                </span>
              </NuxtLink>
            </li> -->

            <!-- <li style="border-right: 1px dashed #ccc; margin: 10px"></li> -->
            <!-- (drop> add a game, connect accounts) -->

            <li class="nav-item">
              <NuxtLink to="/dashboard" class="nav-link">
                <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
                  <Icon>LayoutDashboard</Icon>
                </span>
                <span class="nav-link-title">{{ $t('menu.dashboard') }}</span>
              </NuxtLink>
            </li>

            <li class="nav-item">
              <NuxtLink to="/library" class="nav-link">
                <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
                  <Icon>Apps</Icon>
                </span>
                <span class="nav-link-title">{{ $t('menu.library') }}</span>
              </NuxtLink>
            </li>

            <li class="nav-item">
              <NuxtLink to="/journal" class="nav-link">
                <span class="d-none nav-link-icon d-md-none d-lg-inline-block">
                  <Icon>Notebook</Icon>
                </span>
                <span class="nav-link-title">{{ $t('menu.journal') }}</span>
              </NuxtLink>
            </li>

            <li class="nav-item">
              <NuxtLink to="/import" class="nav-link">
                <span class="nav-link-title text-orange">
                  <Icon>StepInto</Icon>
                  {{ $t('nav.importLibrary') }}
                </span>
              </NuxtLink>
            </li>

            <!-- <li v-if="$app.dev" class="nav-item">
              <NuxtLink to="/welcome" class="nav-link">
                <span class="nav-link-title text-yellow">
                  <Icon>Subtask</Icon>
                  Onboarding
                </span>
              </NuxtLink>
            </li>

            <li v-if="$app.dev" class="nav-item">
              <NuxtLink to="/docs" class="nav-link">
                <span class="nav-link-title text-purple">
                  <Icon>Note</Icon>
                  Documentation
                </span>
              </NuxtLink>
            </li> -->
          </ul>
          <div class="my-2 my-md-0 flex-grow-1 flex-md-grow-0 order-first order-md-last">
            <!-- <div class="input-icon">
              <div
                class="progress progress-sm"
                style="position: absolute; height: 0.15rem">
                <div class="progress-bar progress-bar-indeterminate"></div>
              </div>
              <span class="input-icon-addon">
                <Icon class="mx-2">CloudDown</Icon>
              </span>
              <input
                type="text"
                value="Importing Steam library…"
                class="form-control"
                disabled />
              <span class="input-icon-addon">
                <div
                  class="spinner-border spinner-border-sm text-secondary"
                  role="status"></div>
              </span>
            </div> -->

            <form v-if="$app.dev" action="./" method="get" autocomplete="off" novalidate>
              <div class="input-icon">
                <span class="input-icon-addon">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    class="icon"
                    width="24"
                    height="24"
                    viewBox="0 0 24 24"
                    stroke-width="2"
                    stroke="currentColor"
                    fill="none"
                    stroke-linecap="round"
                    stroke-linejoin="round">
                    <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                    <circle cx="10" cy="10" r="7" />
                    <line x1="21" y1="21" x2="15" y2="15" />
                  </svg>
                </span>

                <div
                  class="input-group input-group-flat"
                  @click.stop="$mitt.emit('search:palette')">
                  <input
                    type="text"
                    class="form-control"
                    autocomplete="off"
                    :placeholder="$t('placeholders.searchAnything')" />
                  <span class="input-group-text">
                    <kbd>CTRL K</kbd>
                  </span>
                </div>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/**
 * @file:    \components\Navigation.vue
 * @desc:    ...
 * -------------------------------------------
 * Created Date: 18th November 2022
 * Modified: Thu 19 December 2024 - 09:59:56
 **/

export default {
  name: 'HeaderNav',
  props: {
    showMobMenu: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {}
  },
}
</script>

<style>
#navbar-menu .navbar {
  z-index: 5;
}
</style>
