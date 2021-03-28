<template>
  <v-app>
      <v-row>
        <v-col lg="3">
          <v-label>
            rowt.ph
          </v-label>
          <v-text-field label="Origin" placeholder="origin" v-model="origin">
          </v-text-field>
          <v-text-field label="Destination" v-model="destination">
          </v-text-field>
          <v-btn color="grey" block @click="reverseLatLng">
            Plan Route
          </v-btn>
          <v-btn color="pink" block>
            Reset
          </v-btn>
        </v-col>
        <v-col>
          <LMap :zoom="zoom" :center="center" @click="updateLatLng"> 
            <l-tile-layer :url="url"></l-tile-layer>
            <l-polyline v-if="cleaned_latlng" :lat-lngs="cleaned_latlng" :color="green"></l-polyline>
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
      test_data: testData,
      cleaned_latlng: [],
      route: []

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
    },
    async getRoute() {
      let coordinates = "Unresolved";
      try {
      const { olat, olng } = this.origin;
      const { dlat, dlng } = this.destination;
      const result = await fetch (
        `https://graphhopper.com/api/1/route?point=${olat},${olng},&point=${dlat},${dlng}&vehicle=bike&locale=en&calc_points=true&points_encoed=false&key=23959cc2-2380-4962-bb26-3746b8d7ff6b`
        );
      if (result.status === 200) {
        const body = await result.json();
        coordinates = body["paths"][0]["points"]["coordinates"][0];
      }
    } catch(e) {
      console.error("Unable to generate route->",e);
    }
    this.route = coordinates;
    alert(route[0]);
    }
  },
};
</script>

<style>
</style>
