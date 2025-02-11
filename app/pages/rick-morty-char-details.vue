<script>
import axios from 'axios'

export default {
  data() {
    return {
      characterId: this.$route.query.id,
      character: null,
      loading: true,
      error: false,
    }
  },
  created() {
    this.fetchCharacterDetails()
  },
  methods: {
    async fetchCharacterDetails() {
      try {
        const response = await axios.get(`https://rickandmortyapi.com/api/character/${this.characterId}`)
        this.character = response.data
      }
      catch (error) {
        console.error('Error fetching character details:', error)
        this.error = true
      }
      finally {
        this.loading = false
      }
    },
    goBack() {
      this.$router.push(`/rick-morty`)
    },
  },
}
</script>

<template>
  <div class="container mx-auto my-5">
    <UCard v-if="character">
      <template #header>
        <div class="flex justify-between items-center">
          <h1 v-if="character" class="text-3xl font-bold">
            {{ character.name }}
          </h1>
          <UButton
            icon="material-symbols:arrow-back-ios-new-rounded"
            size="sm"
            color="primary"
            variant="solid"
            label="Back"
            :trailing="false"
            class="ml-5 rounded-md text-sm font-semibold text-white shadow-xs"
            @click="goBack()"
          />
        </div>
      </template>

      <div class="flex">
        <img v-if="character" :src="character.image" alt="Character Image" class="h-48 shadow-lg mb-6">
        <div v-if="character" class="text-lg ml-5">
          <p><strong>Status:</strong> {{ character.status }}</p>
          <p><strong>Species:</strong> {{ character.species }}</p>
          <p><strong>Gender:</strong> {{ character.gender }}</p>
          <p><strong>Origin:</strong> {{ character.origin.name }}</p>
          <p><strong>Location:</strong> {{ character.location.name }}</p>
          <p><strong>Created:</strong> {{ new Date(character.created).toLocaleDateString() }}</p>
        </div>
      </div>

      <template #footer>
        <div v-if="character" class="mt-6 w-full">
          <h2 class="text-xl font-semibold mb-4">
            Episodes:
          </h2>
          <div class="grid grid-cols-8 gap-2">
            <div v-for="(episode, index) in character.episode" :key="index" class="text-center rounded-md bg-indigo-600 px-3.5 py-2.5 text-sm font-semibold text-white shadow-xs hover:bg-indigo-500 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600">
              Episode {{ episode.split('/').pop() }}
            </div>
          </div>
        </div>
      </template>
    </UCard>

    <div v-if="loading" class="text-center items-center text-xl text-gray-500">
      Loading...
    </div>

    <div v-if="error" class="text-center text-xl text-red-500">
      Error fetching character details.
    </div>
  </div>
</template>
