<script setup>
// ref: This is a Vue function that creates a reactive reference. It's used to create reactive data properties.
import { ref } from "vue";

// lodash: The Lodash library is imported. Lodash is a utility library for working with arrays, objects, and more.
// https://lodash.com/
import lodash from "https://cdn.jsdelivr.net/npm/lodash@4.17.21/+esm";

// msg: A reactive reference holding an empty string. It's used to store the search query entered by the user.
const msg = ref("");

// result: A reactive reference initialized as an empty array. It's intended to store the search results.
const result = ref([]);

// isLoading: A reactive reference initialized as false. It's used to track whether the data is currently being loaded.
const isLoading = ref(false);

// Async Function to Fetch Data:
// searchCountries: An asynchronous function that takes a query parameter. It sends a request to the Restcountries API based on the search query, updates the result and isLoading values accordingly.
const searchCountries = async (query) => {
  if (query.length < 3) {
    return;
  }
  result.value = [];
  try {
    isLoading.value = true;
    const response = await fetch(`https://restcountries.com/v3.1/name/${query}`);
    if (lodash.isArray(result.value)) {
      result.value = await response.json();
      isLoading.value = false;
    }
  } catch {
    result.value = [];
    isLoading.value = false;
  }
}
// Uses Lodash's debounce function to create a debounced version of searchCountries. It delays the execution of searchCountries by 500 milliseconds to avoid excessive API requests.
const debouncedSearch = lodash.debounce(searchCountries, 500);

</script>

<template>
  <!-- v-model="msg": Binds the input field to the msg variable. The user's input will be stored in the msg variable. -->
  <!-- @keydown="searchCountries(msg)": Calls the searchCountries function when a key is pressed. This allows for real-time searching as the user types. -->
  <input placeholder="search" v-model="msg" @keydown="searchCountries(msg)" @input="debouncedSearch(msg)"/>
  <button @click="searchCountries(msg)">Search</button>
  <br />
  <h1>{{ msg }}</h1>
  <!-- <div v-if="lodash.isArray(result)">: Checks if result is an array using Lodash. If true, it iterates over the array and displays the official name of each country. -->
  <div v-if="lodash.isArray(result)">
    <div class="result-item" v-for="item in result" :key="item.cca2">
      {{ item.name.official }}
    </div>
    <!-- <p v-show="isLoading">: Displays a loading message if isLoading is true. -->
    <p v-show="isLoading">is loading ...</p>
  </div>
  <!-- <div v-else>: Displays an error message if the result is not an array. -->
  <div v-else>
    <p class="error">error</p>
  </div>
</template>

<style>
body {
  background-color: aliceblue;
}

input,
button {
  padding: 10px;
  margin-right: 5px;
}

p {
  margin-top: 10px;
  font-style: italic;
}

.error {
  color: #9b0000;
  font-weight: bold;
}

.result-item {
  margin-bottom: 5px;
}
</style>
