<template>
  <div class="page-body">
    <div class="container-xl">
      <div class="row justify-content-center">
        <div class="col-8 row mt-5">
          <div class="col-5 d-none d-md-block">
            <div
              class="card"
              style="border: none"
              @click="set = set == 'one' ? 'two' : 'one'">
              <div
                class="card-body"
                style="
                  background-size: cover;
                  min-height: 350px;
                  outline: 3px solid rgba(255, 255, 255, 0.7);
                  outline-offset: -5px;
                  border: 2px solid black;
                "
                :style="'background-image: url(' + tabs[set][step].image + ')'"></div>
            </div>
            <div class="row pt-2 px-1">
              <div class="col">
                <small class="text-muted">
                  <Icon style="transform: translateY(-1px); margin-right: 2px">
                    Photo
                  </Icon>
                  Image created with AI
                </small>
              </div>
              <div class="col-auto ms-auto">
                <small
                  v-tippy="{
                    content: tabs[set][step].prompt,
                    placement: 'right',
                    theme: 'translucent',
                  }"
                  class="text-muted cursor-help">
                  <Icon style="transform: translateY(-1px); margin-right: 2px">
                    Terminal2
                  </Icon>
                  Prompt
                </small>
              </div>
            </div>
          </div>
          <div class="col col-1 d-none d-md-block"></div>
          <div class="col-12 col-md-5 text-center">
            <div class="row onboarding-steps">
              <div v-for="(tab, i) in tabs.one" :key="'tab' + i" class="col-2">
                <div
                  class="bullet"
                  :class="{ past: i < step, active: i == step }"
                  @click="step = i"></div>
              </div>
            </div>
            <div v-if="step == 0">
              <h1>{{ $t('pages.welcomeTitle') }}</h1>
              <p>
                {{ $t('pages.welcomeParagraph1') }}
              </p>

              <!-- <div class="card-body text-center my-5">
                <span
                  class="avatar avatar-xl rounded"
                  :style="`background-image: url(${$auth.user.avatar})`"></span>
                <div class="card-title mb-1">{{ $auth.user.username }}</div>
              </div> -->

              <div class="btn w-100 my-4" @click="step++">{{ $t('buttons.getStarted') }}</div>
            </div>

            <div v-if="step == 1">
              <h1>{{ $t('pages.everythingYours') }}</h1>

              <p>{{ $t('pages.welcomeParagraph2') }}</p>
              <p>{{ $t('pages.welcomeParagraph3') }}</p>
              <div class="btn w-100 my-4" @click="step++">{{ $t('buttons.continue') }}</div>
            </div>

            <div v-if="step == 2">
              <h1>{{ $t('pages.bringYourGames') }}</h1>
              <p>{{ $t('pages.welcomeParagraph4') }}</p>

              <div class="btn w-100 my-4" @click="step++">{{ $t('buttons.continue') }}</div>
            </div>

            <div v-if="step == 3">
              <h1>{{ $t('pages.keepAJournal') }}</h1>
              <p>{{ $t('pages.welcomeParagraph5') }}</p>
              <div class="btn w-100 my-4" @click="step++">{{ $t('buttons.continue') }}</div>
            </div>

            <div v-if="step == 4">
              <h1>{{ $t('pages.yourGamesYourWay') }}</h1>
              <p>{{ $t('pages.welcomeParagraph6') }}</p>
              <p>{{ $t('pages.welcomeParagraph7') }}</p>
              <b-btn to="import" class="btn btn-primary w-100 my-4">
                <Icon class="me-2">ArrowsTransferDown</Icon>
                {{ $t('buttons.importLibrary') }}
              </b-btn>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/**
 * @file:    \pages\welcome.vue
 * @desc:    ...
 * -------------------------------------------
 * Created Date: 19th January 2024
 * Modified: Thu 19 December 2024 - 10:00:32
 **/

export default {
  name: 'Onboarding',
  setup() {
    const { t } = useI18n()
    definePageMeta({
      title: t('pages.welcomeTitle'),
    })
  },
  data() {
    return {
      step: 0,
      set: 'two',

      tabs: {
        one: [
          {
            image: '/img/illustrations/_545b83a8-77ee-4cf8-a565-995a7c8b5057.jpg',

            prompt: 'You can use lorem ipsum',
          },
          {
            image: 'img/illustrations/_b51641e9-154a-4cc5-a871-4d9258e43f9f.jpeg',

            prompt: 'You can use lorem ipsum',
          },
          {
            image: '/img/illustrations/_78ccd6f6-7612-449e-909f-6784e9f6562b.jpg',

            prompt: 'You can use lorem ipsum',
          },
          {
            image: 'img/illustrations/_b11d2f73-91ab-4c7e-bae2-a93f7abe84c9.jpg',

            prompt: 'You can use lorem ipsum',
          },
          {
            image: 'img/illustrations/_d69c1929-5ff6-4943-88d3-2a86f811e727.jpg',

            prompt: 'You can use lorem ipsum',
          },
        ],
        two: [
          {
            image: '/img/illustrations/_e0724e4c-8e38-4fc4-8b4d-39a5991c20b2.jpg',

            prompt:
              '"Link from Zelda games, arriving to a town. The town has a medieval tone and style. in Viennese secession style, gilded, chromatic, whimsical. Alphonse mucha style"',
          },
          {
            image: 'img/illustrations/_a93d86c4-87c6-4ed3-8ba2-b6f12f67dba8.jpg',

            prompt:
              '"Master chief from halo videogame series, Holding an hologram of data with both hands. The hologram shows the bust of master chief from halo videogame series as well. Medieval tone and style. byzantine Japanese woodblock scene, chromatic. Alphonse mucha style"',
          },
          {
            image: '/img/illustrations/_5fcdad47-41c2-42cc-ad81-532a30957a73.jpg',

            prompt:
              '"Geralt of rivia carrying a wooden box full and overflowing with books. he is in a big library. The box is labeled as games and resembles a treasure chest. byzantine Japanese woodblock scene, gilded, chromatic. Alphonse mucha style"',
          },
          {
            image: 'img/illustrations/_9ce11824-4e49-471a-8887-e67015ac598f.jpg',

            prompt:
              '"Lara croft from Tomb Raider writing in a notebook. she is in a big library. Medieval tone and style. byzantine Japanese woodblock scene, gilded, chromatic, whimsical. Alphonse mucha style"',
          },
          {
            image: 'img/illustrations/_d4780673-addf-46dc-a0cb-cc822fa5a690.jpg',

            prompt:
              '"Cloud Strife holding a closed book with long and colorful notes getting out of the book. He is in a library. byzantine Japanese woodblock scene, in Viennese secession style"',
          },
        ],
      },
    }
  },

  methods: {
    async init() {},
  },

  mounted() {
    this.init()
  },
}
</script>

<style>
.onboarding-steps {
  justify-content: space-evenly;
  margin-top: 20px;
  margin-bottom: 40px;
}

.onboarding-steps .bullet {
  background-color: #ddd;
  border-radius: 100%;
  height: 10px;
  width: 10px;
  cursor: pointer;
  margin: auto;
}

.onboarding-steps .bullet.past {
  background-color: #182433;
}

.onboarding-steps .bullet.active {
  background-color: #446184;
}
</style>
