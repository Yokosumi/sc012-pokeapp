<script lang="ts" setup>
const URL = 'https://pokeapi.co/api/v2/pokemon/ditto'

const { data } = await useFetch(URL)
</script>

<template>
    <div>Some data: {{ (data as any).name }}</div>
</template>

<style scoped></style>
