<script>
import axios from 'axios'
import { onMounted, ref, watch } from 'vue'

export default {
  name: 'CardPage',
  setup() {
    const currentView = ref('grid')
    const cards = ref([])
    const totalPages = ref(0)
    const currentPage = ref(1)
    const searchQuery = ref('')

    const toggleView = (view) => {
      currentView.value = view
    }

    const fetchCharacters = async (page = 1) => {
      try {
        const query = searchQuery.value ? `&name=${searchQuery.value}` : ''
        const offset = (Number.parseInt(page) - 1) * 20
        const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/?offset=${offset}&limit=20${query}`)

        cards.value = response.data.results
        await Promise.all(
          cards.value.map(async (card, index) => {
            const response1 = await axios.get(card.url)
            card.image = response1.data.sprites.front_shiny
            card.id = (Number.parseInt(page) - 1) * 20 + index + 1
          }),
        )
        totalPages.value = response.data.count
      }
      catch (error) {
        console.error('Error fetching data:', error)
        cards.value = []
        totalPages.value = 0
      }
    }

    watch(currentPage, (newPage) => {
      fetchCharacters(newPage)
    })

    onMounted(() => {
      fetchCharacters()
    })
    return {
      totalPages,
      currentPage,
      currentView,
      searchQuery,
      cards,
      toggleView,
      fetchCharacters,
    }
  },
  methods: {
    goToDetail(id) {
      this.$router.push(`/pokemon-char-details?id=${id}`)
    },
  },
}
</script>

<template>
  <div class="container mx-auto my-5">
    <UCard>
      <template #header>
        <div class="flex justify-between items-center">
          <div class="text-2xl">
            Pokeymon
          </div>
          <div class="flex justify-end p-5">
            <!-- <UInput color="gray" variant="outline" placeholder="Search..." class="mr-5 flex" v-model="searchQuery" @keyup="fetchCharacters(1)"/> -->
            <UButton
              icon="material-symbols:grid-view-rounded"
              size="sm"
              color="primary"
              variant="solid"
              label="Grid View"
              :trailing="false"
              class="rounded-md text-sm font-semibold text-white shadow-xs"
              @click="toggleView('grid')"
            />
            <UButton
              icon="material-symbols:lists"
              size="sm"
              color="primary"
              variant="solid"
              label="List View"
              :trailing="false"
              class="ml-5 rounded-md text-sm font-semibold text-white shadow-xs"
              @click="toggleView('list')"
            />
          </div>
        </div>
      </template>

      <div v-if="cards.length === 0" class="text-center text-gray-600 mt-5">
        No characters found.
      </div>

      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 p-5" :class="[currentView === 'grid' ? '' : 'hidden']">
        <UCard v-for="(card, index) in cards" :key="index">
          <template #header>
            <img v-if="card.image" class="mx-auto" :src="card.image" alt="Card image cap">
          </template>

          <h5 class="text-l">
            {{ card.name }}
          </h5>

          <template #footer>
            <div class="flex justify-end">
              <UButton
                v-if="card.image"
                icon="material-symbols:info-outline-rounded"
                size="sm"
                variant="solid"
                label="Details"
                :trailing="false"
                class="text-center rounded-md text-sm font-semibold text-white shadow-xs"
                @click="goToDetail(card.id)"
              />
              <UButton
                v-else
                icon="material-symbols:info-outline-rounded"
                size="sm"
                variant="solid"
                label="No Details"
                :trailing="false"
                class="text-center rounded-md text-sm font-semibold text-white shadow-xs"
              />
            </div>
          </template>
        </UCard>
      </div>

      <div class="flex flex-col gap-4 p-5" :class="[currentView === 'list' ? '' : 'hidden']">
        <UCard v-for="(card, index) in cards" :key="index">
          <template #header>
            <li class="flex">
              <div class="size-12 shrink-0 overflow-hidden rounded-md border border-gray-200">
                <img v-if="card.image" :src="card.image" alt="Card image cap" class="size-full object-cover">
              </div>

              <div class="ml-4 flex flex-1 flex-col">
                <div>
                  <div class="flex justify-between text-base font-medium">
                    <div>
                      <h3>{{ card.name }}</h3>
                    </div>
                    <div>
                      <UButton
                        v-if="card.image"
                        icon="material-symbols:info-outline-rounded"
                        size="sm"
                        variant="solid"
                        label="Details"
                        :trailing="false"
                        class="text-center rounded-md text-sm font-semibold text-white shadow-xs"
                        @click="goToDetail(card.id)"
                      />
                      <UButton
                        v-else
                        icon="material-symbols:info-outline-rounded"
                        size="sm"
                        variant="solid"
                        label="No Details"
                        :trailing="false"
                        class="text-center rounded-md text-sm font-semibold text-white shadow-xs"
                      />
                    </div>
                  </div>
                </div>
              </div>
            </li>
          </template>
        </UCard>
      </div>

      <template #footer>
        <div class="flex justify-end m-5">
          <UPagination v-if="totalPages" v-model="currentPage" size="lg" :total="totalPages" :page-count="20" show-last show-first @update:model-value="fetchCharacters" />
        </div>
      </template>
    </UCard>
  </div>
</template>
