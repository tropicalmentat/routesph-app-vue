<template>
  <v-app>
    <v-container class="pa-0 ma-0" no-gutters fluid>
      <v-row style="height: 600px;">
        <v-col align-self="start" tile>
          <v-sheet height="200">
            <v-text-field label="Origin" v-model="position">
            </v-text-field>
            <v-text-field label="Destination" v-model="position">
            </v-text-field>
          </v-sheet>
        </v-col>
        <v-col>
           <LMap :zoom="zoom" :center="center" @click="updateLatLng"> 
            <l-tile-layer :url="url"></l-tile-layer>
            <l-marker  v-if="position.lat && position.lng" :lat-lng.sync="position" visible draggable></l-marker>
          </LMap>
         
        </v-col>
      </v-row>
    </v-container>

   
  </v-app>
</template>



<script>
import { LMap, LTileLayer, LMarker } from "vue2-leaflet";
// import { LatLng } from "leaflet"


export default {
  components: { LMap, LTileLayer, LMarker },
  data() {
    return {
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      zoom: 13,
      center: [14.599512, 120.984222],
      markerLatLng: [14.599512, 120.984222],
      position: {}

    };
  },
  methods: {
    // getLatLng(e) {
    //   alert(e.latlng.lat + "," + e.latlng.lng)
    //   // _latlng = LMap.latlng(e.latlng.lat,e.latlng.lng)
    // },

    updateLatLng(e) {
      // alert(e.latlng.lat + "," + e.latlng.lng)
      this.position = e.latlng;
    }
  },
};
</script>

<style>

</style>
