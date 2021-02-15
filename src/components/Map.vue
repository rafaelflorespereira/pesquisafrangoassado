<template>
  <div>
    <h2>O restaurante selecionado é o Frango Assado mais próximo de você!</h2>
    <br />
    <label for="autocomplete" style="display: block; font-weight: 600"
      >Selecione seu endereço</label
    >
    <GmapAutocomplete
      id="autocomplete"
      placeholder="Selecione Endereco"
      @place_changed="setPlace"
      :select-first-on-enter="true"
      country="br"
      class="autocomplete"
    ></GmapAutocomplete>
    <GmapMap
      :center="{ lat: userCoordinates.lat, lng: userCoordinates.lng }"
      :zoom="10"
      class="Map"
    >
      <GmapInfoWindow
        :options="infoWindowOptions"
        :position="infoWindowPosition"
        :opened="infoWindowOpened"
        @closeclick="closeRestaurantMarker"
        style="min-width: 100vw;"
      >
        <div class="info-window">
          <h2>{{ activeRestaurant.name }}</h2>
          <br />
          <h5>
            {{ activeRestaurant.endereco }}
          </h5>
          <p>{{ activeRestaurant.cep }}</p>
          <p>{{ activeRestaurant.horario }}</p>
          <p>{{ distanceFromRestaurant }} km</p>

          <p class="ver-maps" @click="redirecionaGoogleMaps()">
            Ver no maps
            <span class="mdi mdi-google-maps"></span>
          </p>
        </div>
      </GmapInfoWindow>
      <GmapMarker :position="userCoordinates" :draggable="true" />
      <!-- These are the restaurant markers -->
      <GmapMarker
        v-for="(restaurant, index) in restaurants"
        :key="index"
        icon="logodofrango.svg"
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
      userCoordinatesInitial: {
        lat: 0,
        lng: 0,
      },
      restaurants: [],
      infoWindowOpened: true,
      infoWindowOptions: {
        pixelOffset: {
          width: 0,
          height: -70,
        },
      },
      activeRestaurant: {
        lat: 0,
        lng: 0,
      },
      closest: 1000000,
      mapCenter: {},
      autoCompleteAdress: "",
    };
  },
  created() {
    this.$getLocation({})
      .then((coordinates) => {
        this.userCoordinatesInitial = coordinates;
      })
      .catch((error) => alert(error));
  },
  mounted() {
    this.restaurants = locations;
    this.restaurants.forEach((r) => {
      const distance = this.getDistanceFromLatLonInKm(
        r.lat,
        r.lng,
        this.userCoordinates.lat,
        this.userCoordinates.lng
      );
      if (distance < this.closest) {
        this.closest = distance;
        this.activeRestaurant = r;
        this.mapCenter = { lat: r.lat, lng: r.lng };
      }
    });
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
    //!CHANGE HERE TO THE EXAT LOCATION LAT AND LNG OF THE ACTIVE RESTAURANT
    //? what format is this place returning?
    //? Does it need to be parsed on any way?
    setPlace(place) {
      this.autoCompleteAdress = place;
    },
    redirecionaGoogleMaps() {
      window.open(
        `https://www.google.com/maps/dir/${this.userCoordinates.lat},${this.userCoordinates.lng}/${this.activeRestaurant.lat},${this.activeRestaurant.lng}`
      );
      console.log(this.activeRestaurant);
    },
  },
  computed: {
    infoWindowPosition() {
      return {
        lat: this.activeRestaurant.lat,
        lng: this.activeRestaurant.lng,
      };
    },
    userCoordinates() {
      if (this.autoCompleteAdress) {
        const lat = this.autoCompleteAdress.geometry["location"].lat();
        const lng = this.autoCompleteAdress.geometry["location"].lng();
        return {
          lat,
          lng,
        };
      } else {
        return this.userCoordinatesInitial;
      }
    },
    distanceFromRestaurant() {
      return this.getDistanceFromLatLonInKm(
        this.activeRestaurant.lat,
        this.activeRestaurant.lng,
        this.userCoordinates.lat,
        this.userCoordinates.lng
      ).toFixed(3);
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
.info-window {
  padding: 0;
  margin: 0;
}
.header-text {
  display: block;
  text-align: left;
  margin: 0 20vw;
}

.autocomplete {
  width: 80vw;
  height: 2rem;
  margin: 1rem;
  padding: 1rem;
  border: solid black 2px;
  border-radius: 100px;
}

@media only screen and (min-width: 600px) {
  .autocomplete {
    width: 40vw;
  }
}

.mdi-google-maps:hover,
.ver-maps:hover {
  color: green;
}
.ver-maps {
  font-style: italic;
  cursor: pointer;
  font-weight: 600;
}
</style>
