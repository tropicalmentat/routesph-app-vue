<!-- TODO
- implement form input validation -> lat,lng formatting | plan route button lat,lng checking before sending route generation request
- fix minor bug in polyline color. doesn't conform to specified color in template
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
					<v-text-field label="Origin" hint="Type in lat,lng" v-model="origin_address" clearable v-on:keyup.enter="convertToOriginLatLng" @click:clear="clearOriginLatLng">
					</v-text-field>
					<v-text-field label="Destination" hint="Type in lat,lng" v-model="destination_address" clearable v-on:keyup.enter="convertToDestinationLatLng" @click:clear="clearDestinationLatLng">
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
					<div>
						Distance:  {{ distance }} km
					</div>
					<div>
						Estimated Travel Time: {{ time }} minutes
					</div>
				</v-card>
				<v-card outlined tile class="pa-2">
					<v-btn color="grey" block @click="getBikeParking">
						Get Parking
					</v-btn>
					<div>
						There are {{ bike_parking.length }} bike parking locations within 200m of your destination
					</div>
				</v-card>
				</v-container>
			</v-col>
			<v-col>
				<LMap :zoom="zoom" :center="center" @click="updateLatLng"> 
					<l-tile-layer :url="url"></l-tile-layer>
					<l-polyline v-if="cleaned_latlng" :lat-lngs="cleaned_latlng" :color="blue"></l-polyline>
					<l-marker  v-if="position.lat && position.lng" :lat-lng.sync="position" visible draggable>
					<l-popup>
						<v-container>
							<v-btn color="green" block @click="setOrigin(); getOriginAddress()">
							Set Origin
							</v-btn>
							<v-btn color="red" block @click="setDestination(); getDestinationAddress()">
							Set Destination
							</v-btn>
						</v-container>
					</l-popup>
					</l-marker>
					<l-marker id="origin" v-if="origin.lat && origin.lng" :lat-lng.sync="origin"></l-marker>
					<l-marker id="destination" v-if="destination.lat && destination.lng" :lat-lng.sync="destination"></l-marker>

					<l-circle-marker v-for="p in bike_parking" v-bind:key="p.id" :lat-lng="p.latlng" :color="red" :fillColor="red"/>

				</LMap>
			</v-col>
		</v-row>
	</v-app>
</template>



<script>
import { LMap, LTileLayer, LMarker, LPopup, LPolyline, LCircleMarker } from "vue2-leaflet";

export default {
  components: { LMap, LTileLayer, LMarker, LPopup, LPolyline, LCircleMarker},
  data() {
    return {
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      zoom: 13,
      center: [14.599512, 120.984222],
      bike_parking_latlng: [14.5909299, 121.0607286],
      position: {},
      origin_txt: "",
      destination_txt: "",
      origin:{},
      destination:{},
      cleaned_latlng: [],
      route: [],
      distance: "",
      time: "",
      bike_parking: [],

      origin_address: "",
      destination_address: ""

    };
  },
  methods: {
    convertToOriginLatLng() {
      let splitText = this.origin_txt.split(",")
      let lat = Number(splitText[0])
      let lng = Number(splitText[1])
      this.origin = {lat,lng}
    },
    convertToDestinationlatlng() {
      let splitText = this.destination_txt.split(",")
      let lat = Number(splitText[0])
      let lng = Number(splitText[1])
      this.destination = {lat,lng}
    },
    clearOriginLatLng() {
      this.origin = ""
      this.cleaned_latlng = []
      this.distance = ""
      this.time = ""
    },
    clearDestinationLatLng() {
      this.destination = ""
      this.cleaned_latlng = []
      this.distance = ""
      this.time = ""
      this.bike_parking = []
    },
    updateLatLng(e) {
      this.position = e.latlng;
    },
    setOrigin() {
      this.origin = this.position
      this.origin_txt = this.position.lat.toFixed(7).toString().concat(",",this.position.lng.toFixed(7).toString())
      this.position = ""
    },
    setDestination() {
      this.destination = this.position
      this.destination_txt = this.position.lat.toFixed(7).toString().concat(",",this.position.lng.toFixed(7).toString())
      this.position = ""
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
      this.origin_address = ""
      this.destination_address = ""
      this.time = ""
      this.bike_parking = []
    },
    async getRoute() {
      let coordinates = "Unresolved";
      let distance = "Unresolved";
      let time = "Unresolved";
      try {
     
      const result = await fetch (
        `http://dev.routes.ph/route?point=${this.origin.lat},${this.origin.lng}&point=${this.destination.lat},${this.destination.lng}&points_encoded=false`
        );
      if (result.status === 200) {
        const body = await result.json();
        coordinates = body["paths"][0]["points"]["coordinates"];
        distance = body["paths"][0]["distance"]
        time = body["paths"][0]["time"]
      }
    } catch(e) {
      alert(e);
    }
    this.route = coordinates;
    this.distance = (distance/1000).toFixed(2);
    this.time = (time/(1000*60)).toFixed(2);

    // This is done to swap lng,lat in the result to lat,lng for the polyline to render
    var coordinate = []

    for (coordinate of coordinates) {
        this.cleaned_latlng.push([coordinate[1],coordinate[0]])
      }
    },
    async getBikeParking() {
      // let coordinates = "Unresolved";
		try {
		const result = await fetch (
			
			`http://dev.routes.ph/get-bike-parking?point=${this.origin.lat},${this.origin.lng}&point=${this.destination.lat},${this.destination.lng}`
			);
		if (result.status === 200) {
			const body = await result.json();
			var item = []
      
			for (item of body) {
				this.bike_parking.push({id:item[0], latlng:[item[1],item[2]]})
			}
      
		}
		} catch(e) {
			alert(e);
		}
    },

  async getOriginAddress() {
    this.loading = true
    let address = "Unresolved address"
    try {
      const result = await fetch (
        `https://nominatim.openstreetmap.org/reverse?format=jsonv2&lat=${this.origin.lat}&lon=${this.origin.lng}`
      );
      if (result.status === 200) {
        const body = await result.json();
        address = body.display_name;
      }
    } catch (e) {
      alert("Reverse Geocode Error",e)
    }
    this.loading = false;
    this.origin_address = address;
  },
 async getDestinationAddress() {
  this.loading = true
  let address = "Unresolved address"
  try {
    const result = await fetch (
      `https://nominatim.openstreetmap.org/reverse?format=jsonv2&lat=${this.destination.lat}&lon=${this.destination.lng}`
    );
    if (result.status === 200) {
      const body = await result.json();
      address = body.display_name;
    }
  } catch (e) {
    alert("Reverse Geocode Error",e)
  }
  this.loading = false;
  this.destination_address = address;
  }
  },
};
</script>

<style>
</style>
