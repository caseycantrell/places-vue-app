<template>
  <div class="home">
    <h1>New Place</h1>

    <ul v-for="error in errors" v-bind:key="error">
      <li>{{ error }}</li>
    </ul>

    <div>
      Name:
      <input type="text" v-model="newPlaceParams.name" />
    </div>
    <div>
      Address:
      <input type="text" v-model="newPlaceParams.address" />
    </div>

    <button v-on:click="createPlace()">Create</button>
    <h1>All Places</h1>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>Name: {{ place.name }}</h2>
      <br />
      <button v-on:click="showPlace(place)">See Details</button>
    </div>

    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Details</h1>
        currentPlace: {{ currentPlace }}
        <p>
          Name:
          <input type="text" v-model="currentPlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="currentPlace.address" />
        </p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        &nbsp;
        <button v-on:click="destroyPlace(currentPlace)">Destroy</button>
        &nbsp;
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      places: [],
      newPlaceParams: {},
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios
        .post("/places", this.newPlaceParams)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      let editPlaceParams = place;
      axios
        .patch(`/places/${place.id}`, editPlaceParams)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function (place) {
      axios.delete(`/places/${place.id}`).then((response) => {
        console.log("Success,", response.data);
        let index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
