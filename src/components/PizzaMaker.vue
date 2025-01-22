<template lang="pug">
  .pizza
    .pizza__base
      transition-group.pizza__ingredients-container(name="ingredient-transition" tag="div")
        .pizza__ingredient(
          v-for="(ingredient, index) in ingredients"
          :key="ingredient.key"
          :style="{ top: ingredient.top + 'px', left: ingredient.left + 'px', transform: 'rotate(' + ingredient.rotation + 'deg)' }")
          img(:src="ingredient.image" :alt="ingredient.name")
</template>

<script setup>
import { onMounted, ref } from 'vue'
import ingredientMushrooms from '@/assets/ingredient-mushrooms.webp'

const ingredients = ref([]) // Array to store ingredients
const maxIngredients = 5 // Limit for each type of ingredient

function addIngredient(name, image) {
  // Check if we need to remove the oldest ingredient of the same type
  if (ingredients.value.filter(ingredient => ingredient.name === name).length >= maxIngredients) {
    const indexToRemove = ingredients.value.findIndex(ingredient => ingredient.name === name)
    if (indexToRemove !== -1) ingredients.value.splice(indexToRemove, 1) // Remove the oldest ingredient of the same type
  }

  // Add new ingredient with random position
  const top = Math.floor(Math.random() * 135)
  const left = Math.floor(Math.random() * 135)
  const rotation = Math.floor(Math.random() * 180) - 90; // Random rotation between -90° and 90°
  const key = top + '-' + left
  ingredients.value.push({ name, image, top, left, rotation, key })
}

onMounted(() => {
  for (let index = 0; index < 10; index++) {
    setTimeout(() => {
      addIngredient('mushroom', ingredientMushrooms)
    }, index * 500);
  }
})
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

  &__ingredients-container {
    position: relative;
    width: 200px;
    height: 200px;
    border-radius: 50%;
    z-index: 3;
  }

  &__ingredient {
    position: absolute;
    width: 50px; /* Adjust ingredient size */
    height: 50px;
    transition: transform 0.5s ease, opacity 0.5s ease;
    z-index: 10; /* Ensure ingredients are visible */

    & img {
      width: 100%;
      height: 100%;
      object-fit: contain;
    }
  }

  /* Ingredient Animation */
  .ingredient-transition-enter-active {
    transform: scale(1);
    opacity: 1;
  }

  .ingredient-transition-enter-from {
    transform: scale(0.5);
    opacity: 0;
  }

  .ingredient-transition-leave-active {
    transform: scale(0.5);
    opacity: 0;
  }
}
</style>
