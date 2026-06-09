<template>
    <div class="w-6/12 m-auto">
        <PostCard 
        :username="props.username"
        :date="props.date"
        :caption="props.caption"
        :photo="props.photo"
        ></PostCard>
    </div>
</template>

<script setup>
    import PostCard from './components/PostCard.vue';
    import { ref } from 'vue';

    const props = ref({
        username: "NARITH HEN",
        date: "09-June-2026",
        caption: "I want to post my faverite food",
        photo: "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/960px-Vue.js_Logo_2.svg.png"
    })
</script>

<style lang="scss" scoped>

</style>