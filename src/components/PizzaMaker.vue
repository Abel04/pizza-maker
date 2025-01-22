<template lang="pug">
  .pizza
    .pizza__base
      transition-group.pizza__ingredients-container(name="ingredient-transition" tag="div")
        .pizza__ingredient(
          v-for="(ingredient, index) in ingredients"
          :key="ingredient.key"
          :style="{ top: ingredient.top + 'px', left: ingredient.left + 'px', transform: 'rotate(' + ingredient.rotation + 'deg)' }")
          img(:src="ingredient.image" :alt="ingredient.name")
  
  .autocomplete(:class="{ 'autocomplete--open': menuOpen }")
    input.autocomplete__input(v-model="typedText" @keydown="handleKeyDown")
    button.autocomplete__clear-button(v-if="typedText" @click="typedText = ''")
      i.material-icons cancel
    ul.autocomplete__menu(v-if="filteredList.length")
      li(
        v-for="(element, i) in filteredList"
        :key="i"
        @click="selectItem(element)"
        :class="{ 'highlighted': highlightedItem === i }")
        p {{ element.label }}
</template>

<script setup>
import { ref, computed } from 'vue'
import ingredientMushrooms from '@/assets/ingredient-mushrooms.webp'
import ingredientPepperoni from '@/assets/ingredient-pepperoni.png'
import ingredientExtraCheese from '@/assets/ingredient-extra-cheese.png'
import ingredientOnions from '@/assets/ingredient-onions.png'
import ingredientSausages from '@/assets/ingredient-sausages.png'
import ingredientOlives from '@/assets/ingredient-olives.png'
import ingredientGreenPeppers from '@/assets/ingredient-green-peppers.png'
import ingredientPineapple from '@/assets/ingredient-pineapple.png'
import ingredientSpinach from '@/assets/ingredient-spinach.png'

const ingredients = ref([]) // Array to store ingredients
const maxIngredients = 10 // Limit for each type of ingredient

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

function addMultipleIngredients(name, image) {
  if (name === 'No Topping at All') {
    ingredients.value = []
    return
  }

  const ingredientsToAdd = Math.floor(Math.random() * 3) + 6 // from 6 to 9 ingredients
  for (let index = 0; index < ingredientsToAdd; index++) {
    setTimeout(() => {
      addIngredient(name, image)
    }, index * 500)
  }
}

let typedText = ref('')
const dropdownElements = ref([
  { label: 'Pepperoni', image: ingredientPepperoni },
  { label: 'Extra Cheese', image: ingredientExtraCheese },
  { label: 'Mushrooms', image: ingredientMushrooms },
  { label: 'Onions', image: ingredientOnions },
  { label: 'Sausages', image: ingredientSausages },
  { label: 'Olives', image: ingredientOlives },
  { label: 'Green Peppers', image: ingredientGreenPeppers },
  { label: 'Pineapple', image: ingredientPineapple },
  { label: 'Spinach', image: ingredientSpinach },
  { label: 'No Topping at All' }
])

const optimizedDropdownElements = computed(() => dropdownElements.value.map(({ label, image }) => {
  return {
    label,
    image,
    optimizedString: label.toLowerCase().normalize("NFKD").replace(/[\u0300-\u036f]/g, "")
  }
}))

const menuOpen = ref(false)
const normalizedKeywords = computed(() => typedText.value.toLowerCase().normalize("NFKD").replace(/[\u0300-\u036f]/g, ""))

const selection = ref('')
const highlightedItem = ref(-1)

const selectItem = item => {
  typedText.value = item.label
  menuOpen.value = false
  addMultipleIngredients(item.label, item.image)
}

const filteredList = computed(() => optimizedDropdownElements.value.filter(item => {
  return item.optimizedString.includes(normalizedKeywords.value)
}))

const handleKeyDown = event => {
  if (!menuOpen.value) menuOpen.value = true
  switch (event.key) {
    case 'ArrowUp':
      highlightedItem.value--
      if (highlightedItem.value < 0) highlightedItem.value = filteredList.value.length - 1
      break
    case 'ArrowDown':
      highlightedItem.value++
      if (highlightedItem.value >= filteredList.value.length) highlightedItem.value = 0
      break
    case 'Enter':
      if (highlightedItem.value >= 0) selectItem(filteredList.value[highlightedItem.value])
      break
    default:
      // Handle other key events if needed
      break
  }
}
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
    /* Adjust ingredient size */
    width: 50px;
    height: 50px;
    transition: transform 0.5s ease, opacity 0.5s ease;
    /* Ensure ingredients are visible */
    z-index: 10;

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

.autocomplete {
  margin: 10px auto;
  max-width: 500px;
  position: relative;
  border: 1px solid rgba(#000, 0.1);
  border-radius: 5px;

  &__input {
    border-radius: inherit;
    padding: 6px 8px;
    width: 100%;
    height: 25px;
    background-color: rgba(#000, 0.5);
    border: none;
    color: #fff;
    transition: 0.3s ease;
    outline: 1px solid rgba(#000, 0);

    &:focus {
      outline: 1px solid rgba(#000, 0.3);
    }
  }

  &__clear-button {
    position: absolute;
    right: 5px;
    top: 0px;
    padding: 0;
    background: none;
    border: none;
    color: #fff;
    cursor: pointer;
    font-size: 16px;
  }

  &--open &__input {
    border-bottom-left-radius: 0;
    border-bottom-right-radius: 0;
  }

  &__menu {
    position: absolute;
    inset: 100% 0 auto;
    list-style-type: none;
    background-color: #ffffffeb;
    z-index: 1;
    border-bottom-left-radius: inherit;
    border-bottom-right-radius: inherit;
    pointer-events: none;
    opacity: 0;
    overflow: auto;
    transition: 0.2s ease-in-out;
    transform: translateY(-0.3em);

    .autocomplete--open & {
      opacity: 1;
      pointer-events: all;
      transform: translateY(0);
    }
  }

  li {
    padding: 4px 8px;
    cursor: pointer;

    &.highlighted {
      background-color: #979f9cea;
      color: #111;
    }
  }
}

@media screen and (max-height: 760px) {
  .autocomplete__menu {
    max-height: 150px;
  }
}
</style>
