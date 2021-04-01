<template>
	<v-app>
		<v-row>
			<v-col lg="3">
				<v-container class="d-flex flex-column">
					<v-label>
						ROWT.PH
					</v-label>
				<v-card outlined tile class="pa-2">
					<v-text-field label="Origin" placeholder="origin" v-model="origin">
					</v-text-field>
					<v-text-field label="Destination" v-model="destination">
					</v-text-field>
				</v-card>
				<v-card outlined tile class="pa-2">
					<v-btn color="green" block @click="getRoute">
						Plan Route
					</v-btn>
				</v-card>
					<v-card outlined tile class="pa-2">
					<v-btn color="pink" @click="resetData" block>
						Reset
					</v-btn>
				</v-card>
				<v-card outlined tile class="pa-2">
					Distance:  {{ distance/1000 }} km
				</v-card>
				</v-container>
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
      cleaned_latlng: [],
      route: [],
      distance: ""
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
    reverseLatLng(coordinates) {
      var coordinate = []

      for (coordinate of coordinates) {
        this.cleaned_latlng.push([coordinate[1],coordinate[0]])
      }
    },
    resetData() {
      this.origin = {}
      this.destination = {}
      this.route = []
      this.cleaned_latlng = []
      this.position = {}
      this.distance = ""
    },
    async getRoute() {
      let coordinates = "Unresolved";
      let distance = "Unresolved";
      try {
      const result = await fetch (
        `https://graphhopper.com/api/1/route?point=${this.origin.lat},${this.origin.lng}&point=${this.destination.lat},${this.destination.lng}&vehicle=bike&locale=en&calc_points=true&points_encoded=false&key=23959cc2-2380-4962-bb26-3746b8d7ff6b`
        );
      if (result.status === 200) {
        const body = await result.json();
        coordinates = body["paths"][0]["points"]["coordinates"];
        distance = body["paths"][0]["distance"]
      }
    } catch(e) {
      alert(e);
    }
    this.route = coordinates;
    this.distance = distance;

    // This is done to swap lng,lat in the result to lat,lng for the polyline to render
    var coordinate = []

      for (coordinate of coordinates) {
        this.cleaned_latlng.push([coordinate[1],coordinate[0]])
      }
    }
  },
};
</script>

<style>
</style>
