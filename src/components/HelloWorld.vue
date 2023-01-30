<template>
  <v-app id="inspire">
    <!-- <v-navigation-drawer v-model="drawer"> -->
      <!--  -->
    <!-- </v-navigation-drawer> -->

    <v-app-bar>
      <!-- <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon> -->

      <v-toolbar-title>メディア芸術企業・団体マップ</v-toolbar-title>
    </v-app-bar>

    <v-main>
      <div id="map"></div>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
  import { Loader } from '@googlemaps/js-api-loader'
  import { ref } from 'vue'

  const drawer = ref(null)

  const locationList = ref([])
  const csvLat = 5
  const csvLng = 6

  // const importCsvFile

  const loader = new Loader({
    apiKey: "",
    version: "weekly",
    libraries: ["places"],
  })

  const mapOptions = {
    center: {
      lat: 0,
      lng: 0
    },
    zoom: 4
  }

  loader
    .load()
    .then((google) => {
      new google.maps.Map(document.getElementById("map"), mapOptions)
    })
    .catch(e => {

    })
</script>
<style>
  #map {
    height: 100%;
    width: 100%;
  }
</style>
