<template>
  <div>
    <h1>
      Your coordinates are Longitude {{ userCoordinates.lng }} Latitude
      {{ userCoordinates.lat }}
    </h1>
    <GmapAutocomplete class="introInput">
      <template v-slot:input="slotProps">
        <v-text-field
          outlined
          prepend-inner-icon="place"
          placeholder="Location Of Event"
          ref="input"
          v-on:listeners="slotProps.listeners"
          v-on:attrs="slotProps.attrs"
        >
        </v-text-field>
      </template>
    </GmapAutocomplete>
    <GmapMap
      :center="{ lat: userCoordinates.lat, lng: userCoordinates.lng }"
      :zoom="15"
      class="Map"
    >
      <GmapInfoWindow
        :options="infoWindowOptions"
        :position="infoWindowPosition"
        :opened="infoWindowOpened"
        @closeclick="closeRestaurantMarker"
      >
        <div class="info-window">
          <h2>{{ activeRestaurant.name }}</h2>
          <h5>{{ activeRestaurant.endereco }}</h5>
          <p>{{ activeRestaurant.cep }}</p>
          <p>{{ activeRestaurant.horario }}</p>
          <p>
            {{
              getDistanceFromLatLonInKm(
                activeRestaurant.lat,
                activeRestaurant.lng,
                userCoordinates.lat,
                userCoordinates.lng
              ).toFixed(3)
            }}
            km
          </p>
        </div>
      </GmapInfoWindow>
      <GmapMarker :position="userCoordinates" :draggable="true" />
      <!-- These are the restaurant markers -->
      <GmapMarker
        v-for="(restaurant, index) in restaurants"
        :key="index"
        :position="{ lat: restaurant.lat, lng: restaurant.lng }"
        :draggable="false"
        :clickable="true"
        @click="openRestaurantMarker(restaurant)"
      />
    </GmapMap>
  </div>
</template>

<script>
import locations from "../data/RestaurantLocations";
export default {
  data: () => {
    return {
      userCoordinates: {
        lat: 0,
        lng: 0,
      },
      restaurants: [],
      infoWindowOpened: false,
      infoWindowOptions: {
        pixelOffset: {
          width: 0,
          height: -35,
        },
      },
      activeRestaurant: {
        lat: 0,
        lng: 0,
      },
    };
  },
  created() {
    //get user coordinates
    //get restaurants coordinates
    //get closest restaurants
    //add google-autcomplete component to make a better user location.
    //this should overwrite the user location
    this.$getLocation({})
      .then((coordinates) => {
        this.userCoordinates = coordinates;
      })
      .catch((error) => alert(error));
  },
  mounted() {
    this.restaurants = locations;
  },
  methods: {
    openRestaurantMarker(restaurant) {
      this.activeRestaurant = restaurant;
      this.infoWindowOpened = true;
    },
    closeRestaurantMarker() {
      this.activeRestaurant = {
        lat: 0,
        lng: 0,
      };
      this.infoWindowOpened = false;
    },
    getDistanceFromLatLonInKm(lat1, lon1, lat2, lon2) {
      var R = 6371; // Radius of the earth in km
      var dLat = this.deg2rad(lat2 - lat1); // deg2rad below
      var dLon = this.deg2rad(lon2 - lon1);
      var a =
        Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.cos(this.deg2rad(lat1)) *
          Math.cos(this.deg2rad(lat2)) *
          Math.sin(dLon / 2) *
          Math.sin(dLon / 2);
      var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
      var d = R * c; // Distance in km
      return d;
    },
    deg2rad(deg) {
      return deg * (Math.PI / 180);
    },
  },
  computed: {
    infoWindowPosition() {
      return {
        lat: this.activeRestaurant.lat,
        lng: this.activeRestaurant.lng,
      };
    },
  },
};
</script>

<style>
.Map {
  width: 80vw;
  height: 60vh;
  position: relative;
  margin: 0 50%;
  left: -40vw;
}
</style>