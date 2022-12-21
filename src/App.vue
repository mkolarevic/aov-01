<script setup>
import { ref, watch } from 'vue'

const searchTerm = ref('')
const suggestions = ref(null)
const timeoutId = ref(null)
const isLoading = ref(false)

const findProducts = async term => {
  fetch(`https://dummyjson.com/products/search?q=${term}`)
    .then(res => res.json())
    .then(value => {
      suggestions.value = value.products
      isLoading.value = false
    })
    .catch(() => {
      alert('There was an issue')
    })
    .finally(() => {
      isLoading.value = false
    })
}

watch(searchTerm, newTerm => {
  if (timeoutId.value) {
    clearTimeout(timeoutId.value)
    timeoutId.value = null
  }
  if (!(newTerm?.trim()?.length === 0)) {
    isLoading.value = true
  } else {
    isLoading.value = false
  }
  timeoutId.value = setTimeout(() => {
    if (newTerm?.trim()?.length === 0 || !newTerm) {
      suggestions.value = []
    } else {
      findProducts(newTerm?.trim())
    }
    clearTimeout(timeoutId.value)
    timeoutId.value = null
  }, 300)
})
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <div v-if="isLoading">loading...</div>
    <ul v-else class="list-disc">
      <li v-for="suggestion in suggestions" :key="suggestion.id" class="flex flex-row">
        <h3>{{ suggestion.title }}</h3>
        <p>(${{ suggestion.price }})</p>
      </li>
    </ul>
  </div>
</template>

<style scoped>
li {
  gap: 8px;
  margin-block: 4px;
  padding: 4px;
}
</style>
