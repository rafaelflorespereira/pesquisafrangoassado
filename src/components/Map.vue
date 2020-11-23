<template>
  <div>
    <h2>O restaurante selecionado é o Frango Assado mais próximo de você!</h2>
    <span class="header-text">
      <h3>Coordenadas do Usuario</h3>
      <p>{{ userCoordinates.lat }}, {{ userCoordinates.lng }}</p>
    </span>
    <span class="header-text">
      <h3>Restaurante Ativo</h3>
      <p>{{ activeRestaurant.lat }}, {{ activeRestaurant.lng }}</p>
    </span>
    <span class="header-text">
      <h3>Distancia do Restaurante</h3>
      <p>{{ distanceFromRestaurant }}</p>
    </span>
    <GmapAutocomplete
      placeholder="Selecione Endereco"
      @place_changed="setPlace"
      :select-first-on-enter="true"
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
        </div>
      </GmapInfoWindow>
      <GmapMarker :position="userCoordinates" :draggable="true" />
      <!-- These are the restaurant markers -->
      <GmapMarker
        v-for="(restaurant, index) in restaurants"
        :key="index"
        icon="logo_frango_assado.svg"
        :position="{ lat: restaurant.lat, lng: restaurant.lng }"
        :draggable="false"
        :clickable="true"
        @click="openRestaurantMarker(restaurant)"
      />
    </GmapMap>
    {{ autoCompleteAdress }}
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
        return this.autoCompleteAdress;
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
  width: 240px;
}
.header-text {
  display: block;
  text-align: left;
  margin: 0 20vw;
}
.autocomplete {
  width: 30vw;
  height: 2rem;
  margin: 1rem;
  padding: 0.25rem;
}
</style>