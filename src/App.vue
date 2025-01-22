<script setup>
import PizzaMaker from './components/PizzaMaker.vue'
</script>

<template lang="pug">
  h1 Pizza Maker
  p Select the toppings by typping them.

  PizzaMaker
</template>

<style lang="scss" scoped>
h1 {
  font-size: 2.5rem;
  color: #dea324;
  font-weight: bold;
  /* Border effect using text-shadow */
  text-shadow:
    -1px -1px 0 #000000,
    1px -1px 0 #000000,
    -1px 1px 0 #000000,
    1px 1px 0 #000000;
}

p {
  font-size: 1.2rem;
  color: #ffffff;
  line-height: 1.6;
  /* Border effect using text-shadow */
  text-shadow:
    -1px -1px 0 #000000,
    1px -1px 0 #000000,
    -1px 1px 0 #000000,
    1px 1px 0 #000000;
}
</style>
