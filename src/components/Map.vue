<!-- eslint-disable vue/multi-word-component-names -->
<script lang="ts" setup>
import { ref, onMounted, watchEffect } from 'vue';
import { useGeolocation } from '@vueuse/core';
import leaflet from 'leaflet';
import 'leaflet/dist/leaflet.css';

const { coords, isSupported, error } = useGeolocation()
const latitude = ref(0)
const longitude = ref(0)

let map: leaflet.Map

watchEffect(()=>{
  if (coords.value.latitude !== Number.POSITIVE_INFINITY && coords.value.longitude !== Number.POSITIVE_INFINITY) {
    latitude.value = coords.value.latitude
    longitude.value = coords.value.longitude
    console.log(latitude.value, longitude.value)

    if (map) {
      map.setView([latitude.value, longitude.value], 13)
    }
  } else {
    console.log('erro')
  }
})

onMounted(() => {
  map = leaflet.map('map').setView([latitude.value, longitude.value], 13)

  leaflet.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
  }).addTo(map);

  if (latitude.value && longitude.value) {
    leaflet.marker([latitude.value, longitude.value]).addTo(map)
  }
})

</script>

<template>
  <div id="map"></div>
  <p>Latitude: {{ coords.latitude }}</p>
  <p>Longitude: {{ coords.longitude }}</p>
  <p>{{ isSupported }}</p>
  <p>{{ error }}</p>
</template>

<style scoped>
#map {
  height: 50vh;
}
</style>
