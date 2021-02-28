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
        <img
          v-for="icon in restaurant.icons"
          :key="icon"
          class="card__info--icons"
          :src="getImgUrl(icon)"
          :alt="icon"
        />

        <p class="card__info--redirect">
          Ir para o maps
          <span class="mdi mdi-google-maps"></span>
        </p>
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
    getImgUrl(icon) {
      return require("../assets/" + icon + ".png");
    },
  },
};
</script>

<style lang="scss">
$animation-delay: 0.5s;
.card-group {
  overflow: auto;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
.card {
  position: relative;
  overflow: auto;
  animation: moveInAnimation ease-in 1s forwards;
  opacity: 0;
  @for $i from 1 through 24 {
    &:nth-child(#{$i}) {
      animation-delay: $i * 0.14s;
    }
  }
  &__info {
    position: relative;
    margin: 2rem;
    width: 18rem;
    height: 14rem;
    background-image: linear-gradient(to right bottom, #f5f5f5, #fff0f5);
    border-radius: 1rem;
    box-shadow: 0 1.5rem 2rem rgba(#000, 0.4);
    padding: 1.4rem;
    transition: all 0.2s;
    @media (max-width: 600px) {
      max-width: 75%;
      height: auto;
      margin: 1rem auto;
    }
    &:hover {
      box-shadow: 0 2.5rem 2.5rem rgba(#000, 0.4);
      transform: translateY(-0.4rem);
      cursor: pointer;
    }

    &--title {
      font-size: 1.2rem;
      font-weight: bold;
      margin-bottom: 0.6rem;
      @media (max-width: 600px) {
        font-size: 1rem;
      }
    }
    &--location {
      font-size: 0.8rem;
      text-transform: capitalize;
      margin-bottom: 0.5rem;
      @media (max-width: 600px) {
        font-size: 0.7rem;
      }
    }
    &--hours {
      font-size: 0.9rem;
      @media (max-width: 600px) {
        font-size: 0.8rem;
      }
    }
    &--icons {
      position: relative;
      margin: 0.8rem 0.2rem 0;
      @media (max-width: 600px) {
        margin: 0.2rem;
      }
    }
    &:hover > &--redirect {
      &::after {
        width: 100%;
      }
    }
    &--redirect {
      margin: 0;
      font-size: 1rem;
      font-weight: 600;
      transition: all 0.2s;
      position: absolute;
      bottom: 1rem;
      left: 50%;
      transform: translateX(-50%);
      @media (max-width: 600px) {
        position: relative;
        transform: translateX(0);
        left: 0;
        bottom: 0;
      }
      &::after {
        content: "";
        display: block;
        width: 0;
        height: 3px;
        background-image: linear-gradient(to right, #a02a2a, #d66d6d);
        transition: all 0.3s;
      }
    }
  }
}

@keyframes moveInAnimation {
  0% {
    opacity: 0;
    transform: translateY(45%);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
