<template>
  <v-app id="inspire">
    <!-- <v-navigation-drawer v-model="store.state.drawer"> -->
      
    <!-- </v-navigation-drawer> -->

    <v-app-bar>
      <!-- <v-app-bar-nav-icon @click="store.setDrawer(!store.state.drawer)"></v-app-bar-nav-icon> -->

      <v-toolbar-title>メディア芸術企業・団体マップ</v-toolbar-title>
    </v-app-bar>

    <v-main>
      <div id="map"></div>
      <!-- <p>{{ store.state.locationList }}</p> -->
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
// <script setup>
  import { Loader } from '@googlemaps/js-api-loader'
  import { MarkerClusterer } from '@googlemaps/markerclusterer'
  import { ref, reactive } from 'vue'
  import Papa from 'papaparse'
  import { isPresent, isDefined, isFilled } from 'ts-is-present'

  interface Location {
    name: string,
    url: string,
    latlng: {
      lat: number,
      lng: number
    }
  }

  const store = {
    state: reactive({
      locationList: [] as Array<Location>,
      drawer: false,
    }),
    setLocationList(newValue: Array<Location>) {
      this.state.locationList = newValue
    },
    setDrawer(newValue: boolean) {
      this.state.drawer = newValue
    }
  }  
  
  const parseCsv = Papa.parse<any>('./src/assets/address_withlatlng.csv', {
    download: true,
    header: true,
    complete: function(results){
      const locationList: Array<Location> = results.data.map(result => {
        return {
          name: result['ラベル'],
          url: result['アイテム'],
          latlng: {
            lat: Number(result['緯度']), 
            lng: Number(result['経度'])
          }
        }
      })
      // console.log(locationList)
      store.setLocationList(locationList)
    }
  })

  const loader = new Loader({
    apiKey: import.meta.env.VITE_API_KEY,
    version: "weekly",
    libraries: ["places"],
  })

  const mapOptions = {
    zoom: 5,
    center: {
      lat: 35.65629,
      lng: 139.74142
    }
  }

  loader
    .load()
    .then((google) => {
      const map = new google.maps.Map(
        document.getElementById("map") as HTMLElement, 
        mapOptions
      )
      const markers = store.state.locationList.map((position) => {
        const contentStr = '<a href="' + position['url'] + '" target=_blank>' + position['name'] + '</a>'
        const infoWindow = new google.maps.InfoWindow({
          content: contentStr
        })
        const marker = new google.maps.Marker({
          position: position['latlng']
        })

        marker.addListener("click", () => {
          infoWindow.open(map, marker)
        })

        return marker    
      })
      new MarkerClusterer({ markers, map})
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
