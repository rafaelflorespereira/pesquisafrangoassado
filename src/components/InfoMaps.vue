<template>
  <div class="card-group">
    <div v-for="restaurant in restaurants" :key="restaurant.name" class="card">
      <div class="card__info">
        <div class="card__info--title" v-text="restaurant.name"></div>
        <div class="card__info--location" v-text="restaurant.endereco"></div>
        <div class="card__info--hours" v-text="restaurant.horario"></div>
      </div>
    </div>
  </div>
</template>

<script>
import locations from "../data/RestaurantLocations";
export default {
  data: () => {
    return {
      restaurants: [],
    };
  },
  mounted() {
    this.restaurants = locations;
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
    }
    &--title {
      font-size: 1.6rem;
      font-weight: bold;
      margin-bottom: 2rem;
    }
    &--location {
      text-transform: capitalize;
      margin-bottom: 2rem;
    }
    &--hours {
      font-size: 1.2rem;
    }
  }
}
</style>
