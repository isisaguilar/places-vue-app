<template>
  <div class="home">
    <h1>New Place</h1>
    <div>
      Name:
      <input type="text" v-model="newPlace.name" />
    </div>
    <div>
      Address:
      <input type="text" v-model="newPlace.address" />
    </div>
    <button v-on:click="createPlace">Create Place</button>
    <p>New Place: {{ newPlace }}</p>
    <div v-for="place in places" v-bind:key="place.id">
      <h1>{{ place.name }}</h1>
      <button v-on:click="showPlace(place)">Show Details</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Details</h1>
        currentPlace: {{ currentPlace }}
        <p>
          Name:
          <input type="text" v-model="currentPlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="currentPlace.address" />
        </p>
        <button v-on:click="updatePlace(currentPlace)">Update</button> <br />
        <button v-on:click="destroyPlace(currentPlace)">Destroy</button> <br />
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
      newPlace: {},
      currentPlace: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios
        .post("/places", this.newPlace)
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
      var updatePlace = place;
      axios
        .patch(`/places/${place.id}`, updatePlace)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function (place) {
      axios.delete(`/places/${place.id}`).then((response) => {
        console.log("Place has been destroyed,", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
