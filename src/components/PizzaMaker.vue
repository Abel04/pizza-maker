<template lang="pug">
  .pizza
    .pizza__base
</template>

<script setup>
</script>

<style lang="scss">
.pizza {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
  font-family: Arial, sans-serif;

  /* Pizza Base with Image */
  &__base {
    width: 200px;
    height: 200px;
    border-radius: 50%;
    position: relative;
    margin-bottom: 20px;
    background: url('@/assets/pizza.webp') no-repeat center center;
    background-size: cover;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
    overflow: hidden;
  }
}
</style>
