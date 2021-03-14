<template>
  <LMap style="height: 100%" :zoom="zoom" :center="center" @click="getLatLng">
    <l-tile-layer :url="url"></l-tile-layer>
    <l-marker :lat-lng="markerLatLng" ></l-marker>
  </LMap>
</template>

<script>
import { LMap, LTileLayer, LMarker } from "vue2-leaflet";
// import { OriginInputBox } from "./components/OriginInputBox.vue"


export default {
  components: { LMap, LTileLayer, LMarker },
  data() {
    return {
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      zoom: 13,
      center: [14.599512, 120.984222],
      markerLatLng: [14.599512, 120.984222]
    };
  },
  methods: {
    getLatLng(e) {
      alert(e.latlng.lat + "," + e.latlng.lng)
    }
  }
};
</script>

<style>
body {
  padding: 0;
  margin: 0;
}
html,
body,
map {
  height: 100%;
  width: 100%;
}
</style>
