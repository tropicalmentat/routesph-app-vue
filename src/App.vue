<template>
  <v-app>
      <v-row>
        <v-col lg="3">
          <v-label>
            rowt.ph
          </v-label>
          <v-text-field label="Origin" v-model="origin">
          </v-text-field>
          <v-text-field label="Destination" v-model="destination">
          </v-text-field>
          <v-btn color="grey" block @click="reverseLatLng">
            Plan Route
          </v-btn>
        </v-col>
        <v-col>
          <LMap :zoom="zoom" :center="center" @click="updateLatLng"> 
            <l-tile-layer :url="url"></l-tile-layer>
            <l-polyline v-if="cleaned_latlng" :lat-lngs="cleaned_latlng" :color="route.color"></l-polyline>
            <l-marker  v-if="position.lat && position.lng" :lat-lng.sync="position" visible draggable>
              <l-popup>
                <v-container>
                  <v-btn color="green" block @click="setOrigin">
                    Set Origin
                  </v-btn>
                  <v-btn color="red" block @click="setDestination">
                    Set Destination
                  </v-btn>
                </v-container>
              </l-popup>
            </l-marker>
            <l-marker id="origin" v-if="origin.lat && origin.lng" :lat-lng.sync="origin"></l-marker>
            <l-marker id="destination" v-if="destination.lat && destination.lng" :lat-lng.sync="destination"></l-marker>
            
          </LMap>
        </v-col>
      </v-row>
  </v-app>
</template>



<script>
import { LMap, LTileLayer, LMarker, LPopup, LPolyline} from "vue2-leaflet";
import testData from "./data/test_polyline.json"

export default {
  components: { LMap, LTileLayer, LMarker, LPopup, LPolyline},
  data() {
    return {
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      zoom: 13,
      center: [14.599512, 120.984222],
      markerLatLng: [14.599512, 120.984222],
      position: {},
      origin:{},
      destination:{},
      route:{
        latlngs:[[14.602685,121.033665],[14.552686,121.051559]],
        color: 'green'
      },
      test_data: testData,
      cleaned_latlng: []

    };
  },
  methods: {
    updateLatLng(e) {
      this.position = e.latlng;
    },
    setOrigin() {
      this.origin = this.position;
      this.originMarker.push(this.position)
    },
    setDestination() {
      this.destination = this.position;
    },
    reverseLatLng() {
      var coordinate = []

      for (coordinate of this.test_data["paths"][0]["points"]["coordinates"]) {
        this.cleaned_latlng.push([coordinate[1],coordinate[0]])
      }

      alert(this.cleaned_latlng[0]);
    }
  },
};
</script>

<style>
</style>
