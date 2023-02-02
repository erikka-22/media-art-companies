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
      <!-- <p>{{ store.state.locationList }}</p> -->
    </v-main>
  </v-app>
</template>

<!-- <script setup lang="ts"> -->
<script setup>
  import { Loader } from '@googlemaps/js-api-loader'
  import { ref, reactive } from 'vue'
  import Papa from 'papaparse'

  const drawer = ref(null)

  const store = {
    state: reactive({
      locationList: {}
    }),
    setLocationList(newValue) {
      this.state.locationList = newValue
    }
  }
  
  const csvOptions = {
    csvLat: 5,
    csvLng: 6,
  }
  
  const dropLocationPoints = () => {

  }

  const parseCsv = Papa.parse('./src/assets/address_withlatlng.csv', {
    download: true,
    header: true,
    complete: function(results){
      store.setLocationList(results.data)
    }
  })
  // console.log(store.state.locationList)

  const loader = new Loader({
    apiKey: import.meta.env.VITE_API_KEY,
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
