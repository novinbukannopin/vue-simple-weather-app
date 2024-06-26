<script setup lang="ts">

import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { faMagnifyingGlass } from '@fortawesome/free-solid-svg-icons'
import { reactive } from 'vue'

const emits = defineEmits(['place-data'])

interface SearchTerm {
    query: string
    timeout: number | undefined
    results: any
}

const searchTerm: SearchTerm = reactive({
    query: '',
    timeout: undefined,
    results: null
})

const handleSearch = () => {
    clearTimeout(searchTerm.timeout)
    searchTerm.timeout = setTimeout(async () => {
        if(searchTerm.query !== '') {
          const res = await fetch(`http://api.weatherapi.com/v1/search.json?key=34abfc2fda594c68baa31412240504&q=${searchTerm.query}`, {
            referrerPolicy: "unsafe-url"
          })
          searchTerm.results = await res.json()
        } else {
          searchTerm.results = null
        }
    }, 500)
}

const getWeather = async (id: string) => {
    const res = await fetch(`http://api.weatherapi.com/v1/forecast.json?key=34abfc2fda594c68baa31412240504&q=id:${id}&days=3&aqi=no&alerts=no`, {
      referrerPolicy: "unsafe-url"
    })
    const data = await res.json()
    emits('place-data', data)

    searchTerm.query = ''
    searchTerm.results = null
}

</script>

<template>
  <div>

    <form>
      <div class="bg-white border border-indigo-600/30 rounded-lg shadow-lg flex items-center">
        <FontAwesomeIcon :icon="faMagnifyingGlass" class="text-indigo-600 ml-2 p-2"/>
        <input
          type="text"
          placeholder="Search for a country"
          class="rounded-r-lg p-2 border-0 outline-0 focus:ring-2 focus:ring-indigo-600 ring-inset w-full"
          v-model="searchTerm.query"
          @input="handleSearch"
        />

      </div>
    </form>

    <div class="bg-white my-2 rounded-lg shadow-lg">
      <div v-if="searchTerm.results !== null">
        <div v-for="place in searchTerm.results" :key="place.id">
          <button @click="getWeather(place.id)" class="px-3 my-2 hover:text-indigo-600 hover:font-bold w-full text-left">
            {{ place.name }}, {{ place.region }}
          </button>
        </div>
      </div>
    </div>

  </div>
</template>