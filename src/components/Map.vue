<!-- eslint-disable vue/multi-word-component-names -->
<script lang="ts" setup>
import { ref, onMounted, watchEffect } from 'vue';
import { useGeolocation } from '@vueuse/core';
import leaflet from 'leaflet';
import 'leaflet/dist/leaflet.css';
import { VCard, VCardText, VCardTitle, VCol, VRow } from 'vuetify/components';

const { coords } = useGeolocation()
const latitude = ref(0)
const longitude = ref(0)
const zoomMap = ref(22)

let map: leaflet.Map
let marker: leaflet.Marker

watchEffect(()=>{
  // Verifica se os valores da latitude e longitude no coords são diferentes de Infinity
  if (coords.value.latitude !== Number.POSITIVE_INFINITY && coords.value.longitude !== Number.POSITIVE_INFINITY) {
    latitude.value = coords.value.latitude // Armazena a latitude do usuário na variável reativa (latitude)
    longitude.value = coords.value.longitude // Armazena a longitude do usuário na variável reativa (longitude)

    if (map) {
      // Pega a localização do usuário e cria o mapa
      map.setView([latitude.value, longitude.value], zoomMap.value)

      // Remove o marcador caso ele exista
      if (marker) {
        marker.remove()
      }

      // Adiciona um marcador no mapa
      marker = leaflet.marker([latitude.value, longitude.value]).addTo(map)
    }
  } else {
    console.log('erro') // Exibe uma mensagem de erro no console, caso não consiga pegar a localização do usuário
  }
})

onMounted(() => {
  // Cria o mapa na montagem no site
  map = leaflet.map('map').setView([latitude.value, longitude.value], zoomMap.value)

  // Direitos autorais dos criadores
  leaflet.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
  }).addTo(map);
})

</script>

<template>
  <VContainer>
    <div id="map"></div>
    <h2 class="text-center my-4">Essa é sua localização</h2>
    <VRow>
      <VCol cols="12" md="6">
        <VCard>
          <VCardTitle>Latitude:</VCardTitle>
          <VCardText class="text-subtitle-1">{{ coords.latitude !== Number.POSITIVE_INFINITY ? coords.latitude : 0 }}</VCardText>
        </VCard>
      </VCol>
      <VCol cols="12" md="6">
        <VCard>
          <VCardTitle>Longitude:</VCardTitle>
          <VCardText class="text-subtitle-1">{{ coords.longitude !== Number.POSITIVE_INFINITY ? coords.longitude : 0 }}</VCardText>
        </VCard>
      </VCol>
    </VRow>
  </VContainer>
</template>

<style scoped>
#map {
  height: 50vh;
}
</style>
