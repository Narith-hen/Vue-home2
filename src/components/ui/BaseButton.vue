<template>
    <div class="flex ">
        <button class="py-4 px-6 bg-green-500" @click="click()">Click</button>
    </div>
</template>

<script setup>
import { defineEmits } from 'vue';
const emit = defineEmits(['clickLike']);

const click = () => {
    emit('clickLike');
}

</script>

<style lang="scss" scoped>
</style>