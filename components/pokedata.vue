<script lang="ts" setup>
import { ref, onMounted, capitalize } from 'vue'
import axios from 'axios'

interface Pokemon {
    name: string
    url: string
    details: any // Replace 'any' with the actual type of the details
}

interface PokemonData {
    count: number
    next: string
    previous: string
    results: Pokemon[]
}

const URL = 'https://pokeapi.co/api/v2/pokemon?limit=10&offset=0'
const data = ref<PokemonData | null>(null)

onMounted(async () => {
    const response = await axios.get<PokemonData>(URL)
    const details = await Promise.all(
        response.data.results.map(async (pokemon) => {
            const detailResponse = await axios.get(pokemon.url)
            return detailResponse.data
        })
    )
    data.value = {
        ...response.data,
        results: response.data.results.map((pokemon, index) => ({
            ...pokemon,
            details: details[index],
        })),
    }
})
</script>

<template>
    <div v-for="pokemon in data?.results" :key="pokemon.name">
        <li>
            {{ capitalize(pokemon.name) }}
        </li>
        <li>{{ capitalize(pokemon.details.types[0].type.name) }}</li>
        <li>
            {{ pokemon.details.stats[0].stat.name.toUpperCase() }}:
            {{ pokemon.details.stats[0].base_stat }}
        </li>
    </div>
</template>

<style scoped></style>
