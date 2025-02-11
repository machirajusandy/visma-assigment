<template>
    <div class="container mx-auto my-5">
        <UCard v-if="character">
            <template #header>
                <div class="flex justify-between items-center">
                    <h1 v-if="character" class="capitalize text-3xl font-bold">{{ character.name }}</h1>
                    <UButton
                        icon="material-symbols:arrow-back-ios-new-rounded"
                        size="sm"
                        color="primary"
                        variant="solid"
                        label="Back"
                        :trailing="false"
                        @click="goBack()"
                        class="ml-5 rounded-md text-sm font-semibold text-white shadow-xs"
                    />
                </div>
            </template>

            <div class="flex">
                <div v-if="character" class="grid grid-cols-2 gap-2">
                    <img v-if="character" :src="character.sprites.front_shiny" alt="Character Image" class="shadow-lg mb-2" />
                </div>
                <div v-if="character" class="text-lg ml-5">
                    <p><strong>Experience:</strong> {{ character.base_experience }}</p>
                    <p><strong>Height:</strong> {{ character.height }}</p>
                    <p><strong>Weight:</strong> {{ character.weight }}</p>
                    <div class="flex">
                        <strong class="mr-1">Abilities:</strong>
                        <ul>
                            <li v-for="(ability, index) in character.abilities" :key="index">({{ index+1 }}) {{ ability.ability.name }}</li>
                        </ul>
                    </div>
                </div>
            </div>
            <hr>
            <div>
                <div v-if="character" class="mt-6 w-full">
                    <div v-for="(other, index) in character.sprites.other" :key="index">
                        <h2 class="capitalize text-xl font-semibold mb-4">{{ index.replace(/[_-]/g, ' ') }}</h2>
                        <div class="grid grid-cols-8 gap-2">
                            <div v-for="(image, ind) in other" :key="ind">
                                <img v-if="image" :src="image" alt="Character Image" class="shadow-lg mb-2" />
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </UCard>

        <div v-if="loading" class="text-center items-center text-xl text-gray-500">Loading...</div>

        <div v-if="error" class="text-center text-xl text-red-500">Data Not Found</div>
    </div>
</template>

  
<script>
    import axios from 'axios';

    export default {
        data() {
            return {
                characterId: this.$route.query.id,
                character: null,
                loading: true,
                error: false,
            };
        },
        created() {
            this.fetchCharacterDetails();
        },
        methods: {
            async fetchCharacterDetails() {
                try {
                    const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${this.characterId}`);
                    this.character = response.data;
                } catch (error) {
                    console.error('Error fetching character details:', error);
                    this.error = true;
                } finally {
                    this.loading = false;
                }
            },
            goBack(){
                this.$router.push(`/pokemon`);
            }
        }
    };
</script>

  