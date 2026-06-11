<template>
  <div class="text-center">
    <h1>It show on the console "Narith"</h1>
    <BaseButton @clickLike="hanldeClick()"></BaseButton>
  </div>
</template>

<script setup>
import BaseButton from './components/ui/BaseButton.vue'

const hanldeClick = () => {
  console.log('Narith')
}
</script>

<style lang="scss" scoped></style>
