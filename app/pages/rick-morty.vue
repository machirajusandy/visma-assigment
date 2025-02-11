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
        const response = await axios.get(`https://rickandmortyapi.com/api/character?page=${page}${query}`)

        cards.value = response.data.results
        totalPages.value = response.data.info.count
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
      this.$router.push(`/rick-morty-char-details?id=${id}`)
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
            Rick Morty Characters
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
            <img class="h-48 mx-auto" :src="card.image" alt="Card image cap">
          </template>

          <h5 class="text-l">
            {{ card.name }}
          </h5>

          <template #footer>
            <div class="flex justify-end">
              <UButton
                icon="material-symbols:info-outline-rounded"
                size="sm"
                variant="solid"
                label="Details"
                :trailing="false"
                class="text-center rounded-md text-sm font-semibold text-white shadow-xs"
                @click="goToDetail(card.id)"
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
                <img :src="card.image" alt="Card image cap" class="size-full object-cover">
              </div>

              <div class="ml-4 flex flex-1 flex-col">
                <div>
                  <div class="flex justify-between text-base font-medium">
                    <div>
                      <h3>{{ card.name }}</h3>
                      <p class="mt-1 text-sm">
                        {{ card.status }}
                      </p>
                    </div>
                    <div>
                      <UButton
                        icon="material-symbols:info-outline-rounded"
                        size="sm"
                        variant="solid"
                        label="Details"
                        :trailing="false"
                        class="text-center rounded-md text-sm font-semibold text-white shadow-xs"
                        @click="goToDetail(card.id)"
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
