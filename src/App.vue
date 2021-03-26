<template>
  <v-app>
      <v-row>
        <v-col lg="3">
            <v-label>
              rowt.ph
            </v-label>
            <v-text-field label="Origin" v-model="origin">
            </v-text-field>
            <v-text-field label="Destination">
            </v-text-field>
        </v-col>
        <v-col>
          <LMap :zoom="zoom" :center="center" @click="updateLatLng"> 
            <l-tile-layer :url="url"></l-tile-layer>
            <l-marker  v-if="position.lat && position.lng" :lat-lng.sync="position" visible draggable>
              <l-popup>
                <V-container>
                  <v-btn color="green" click="setOrigin=">
                    Set Origin
                  </v-btn>
                  <v-btn color="red">
                    Set Destination
                  </v-btn>
                </v-container>
              </l-popup>
            </l-marker>
          </LMap>
        </v-col>
      </v-row>
  </v-app>
</template>



<script>
import { LMap, LTileLayer, LMarker, LPopup } from "vue2-leaflet";
// import { LatLng } from "leaflet"


export default {
  components: { LMap, LTileLayer, LMarker, LPopup },
  data() {
    return {
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      zoom: 13,
      center: [14.599512, 120.984222],
      markerLatLng: [14.599512, 120.984222],
      position: {},
      origin:{}

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
    },
    setOrigin(e) {
      this.origin = e.latlng;
    }
  },
};
</script>

<style>

</style>
