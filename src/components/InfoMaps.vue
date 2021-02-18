<template>
  <div class="card-group">
    <div v-for="restaurant in restaurants" :key="restaurant.name" class="card">
      <div
        class="card__info"
        @click="redirecionaGoogleMaps(restaurant.lat, restaurant.lng)"
      >
        <div class="card__info--title" v-text="restaurant.name"></div>
        <div class="card__info--location" v-text="restaurant.endereco"></div>
        <div class="card__info--hours" v-text="restaurant.horario"></div>
        <div class="card__info--redirect">
          Ir para o maps <span class="mdi mdi-google-maps"></span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import locations from "../data/RestaurantLocations";
export default {
  props: ["userDirections"],
  data: () => {
    return {
      restaurants: [],
    };
  },
  mounted() {
    this.restaurants = locations;
  },
  methods: {
    redirecionaGoogleMaps(lat, lng) {
      window.open(
        `https://www.google.com/maps/dir/${this.userDirections.lat},${this.userDirections.lng}/${lat},${lng}`
      );
    },
  },
};
</script>

<style lang="scss">
.card-group {
  overflow: auto;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
.card {
  position: relative;
  &__info {
    margin: 2rem;
    width: 22rem;
    height: 15rem;
    background-image: linear-gradient(to right bottom, #f5f5f5, #fff0f5);
    border-radius: 1rem;
    box-shadow: 0 1.5rem 2rem rgba(#000, 0.4);
    padding: 1rem;
    transition: all 0.2s;
    @media (max-width: 600px) {
      width: 80%;
    }
    &:hover {
      box-shadow: 0 2.5rem 2.5rem rgba(#000, 0.4);
      transform: translateY(-0.4rem);
      cursor: pointer;
    }
    &:hover > &--redirect {
      &::after {
        width: 100%;
      }
    }
    &--title {
      font-size: 1.6rem;
      font-weight: bold;
      margin-bottom: 2rem;
    }
    &--location {
      text-transform: capitalize;
      margin-bottom: 1rem;
    }
    &--hours {
      font-size: 1.2rem;
    }
    &--redirect {
      margin: 0.5rem 4rem;
      font-size: 1.2rem;
      font-weight: 600;
      transition: all 0.2s;
      &::after {
        content: "";
        display: block;
        width: 0;
        height: 3px;
        background-image: linear-gradient(to right, #a02a2a, #d66d6d);
        transition: width 0.3s;
      }
    }
  }
}
</style>
