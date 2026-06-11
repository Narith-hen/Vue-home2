<template>
    <div>
        <h1>{{ username }}</h1>
        <h1>{{ date }}</h1>
        <h1>{{ caption }}</h1>
        <img :src="photo" class="w-32" alt="vue image" >
    </div>
</template>

<script setup>
defineProps({
    username: String,
    date: String,
    caption: String,
    photo: String
})

</script>

<style lang="scss" scoped>

</style>