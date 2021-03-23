<template>
  <v-app>
    <v-container>
      <v-row>
        <v-col>
          <v-text-field label="Origin">
          </v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field label="Destination">
          </v-text-field>
        </v-col>
      </v-row>
    </v-container>

    <LMap style="height: 100%" :zoom="zoom" :center="center" @click="updateLatLng"> 

      <l-tile-layer :url="url"></l-tile-layer>
      <l-marker  v-if="position.lat && position.lng" :lat-lng.sync="position" visible draggable></l-marker>
    </LMap>
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
body {
  padding: 0;
  margin: 0;
}
html,
body,
#map {
  height: 100%;
  width: 100%;
}
</style>
