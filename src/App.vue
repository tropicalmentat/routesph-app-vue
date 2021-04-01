<!-- TODO
- Bind text in text field with orig and dest marker lat lng using key on enter event and clear event https://stackoverflow.com/questions/53825246/bind-multiple-events-to-v-on-directive-in-vue
- Make text input text fields and orig and dest markers binded to the same state
- fix minor bug in polyline color. doesn't conform to specified color in template
- route distance text should be 2 decimal places
- color code origin and destination markers
- change selection marker icon
- add feature to switch origin and destination
- add feature to conditionally render input text fields for orig and dest lat,lng OR address (will need to implement geocoding first)
- add estimate time of travel
- improve banner text OR use image
- build own tileserver and style (big effort to dev!)
 -->
<template>
	<v-app>
		<v-row>
			<v-col lg="3">
				<v-container>
					<v-label>
						ROWT.PH
					</v-label>
				<v-card outlined tile class="pa-2">
					<v-text-field label="Origin" hint="Type in lat,lng" v-model="origin_txt" clearable v-on:keyup.enter="convertTooriginlatlng">
					</v-text-field>
					<v-text-field label="Destination" hint="Type in lat,lng" v-model="destination_txt" clearable v-on:keyup.enter="convertTodestinationlatlng">
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
				<LMap :zoom="zoom" :center="center" @dblclick="updateLatLng"> 
					<l-tile-layer :url="url"></l-tile-layer>
					<l-polyline v-if="cleaned_latlng" :lat-lngs="cleaned_latlng" :color="red"></l-polyline>
					<l-marker  v-if="position.lat && position.lng" :lat-lng.sync="position" visible draggable>
					<l-tooltip>Drag me! Then click!</l-tooltip>
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
import { LMap, LTileLayer, LMarker, LPopup, LPolyline, LTooltip} from "vue2-leaflet";

export default {
  components: { LMap, LTileLayer, LMarker, LPopup, LPolyline, LTooltip },
  data() {
    return {
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      zoom: 13,
      center: [14.599512, 120.984222],
      markerLatLng: [14.599512, 120.984222],
      position: {},
      origin_txt: "",
      destination_txt: "",
      origin:{},
      destination:{},
      cleaned_latlng: [],
      route: [],
      distance: ""
    };
  },
  methods: {
    convertTooriginlatlng() {
      let splitText = this.origin_txt.split(",")
      let lat = Number(splitText[0])
      let lng = Number(splitText[1])
      this.origin = {lat,lng}
    },
    convertTodestinationlatlng() {
      let splitText = this.destination_txt.split(",")
      let lat = Number(splitText[0])
      let lng = Number(splitText[1])
      this.destination = {lat,lng}
    },
    updateLatLng(e) {
      this.position = e.latlng;
    },
    setOrigin() {
      this.origin = this.position;
      this.origin_txt = this.position.lat.toString().concat(",",this.position.lng.toString())
    },
    setDestination() {
      this.destination = this.position;
      this.destination_txt = this.position.lat.toString().concat(",",this.position.lng.toString())
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
      this.origin_txt = ""
      this.destination_txt = ""
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
