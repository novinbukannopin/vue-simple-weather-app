<script setup lang="ts">
import { ref } from 'vue'
import WeatherCard from '@/components/WeatherCard.vue'
import SearchInput from '@/components/SearchInput.vue'
import type { WeatherData } from '@/types/weather'

const places = ref<WeatherData[]>([]) // Menggunakan tipe Place yang didefinisikan secara global

const addPlace = (data: WeatherData) => {
  places.value.push(data)
}

const deletePlace = (name: string) => {
  if (confirm('Are you sure')) {
    places.value = places.value.filter((p) => p.location.name !== name)
  }
}
</script>

<template>
  <main>
    <div class="text-center mb-6">
      {{ new Date().toLocaleString('en-us', {
      weekday: 'long',
      month: 'long',
      day: 'numeric',
      year: 'numeric',
    })
      }}
    </div>

    <div>
      <SearchInput @place-data="addPlace" />
    </div>

    <div class="grid grid-cols-2 gap-4">
      <div v-for="(place, idx) in places" :key="idx">
        <WeatherCard :place="place" @delete-place="deletePlace" />
      </div>
    </div>
  </main>
</template>
