<template>
  <div class="home">
    <div>
      Name: <input type="text" v-model="name"><br>
      Address: <input type="text" v-model="address"><br>
      <button v-on:click="createPlace()">Create Place</button>
      <p v-for="error in errors">{{ error }}</p>
    </div>
    <div v-for="place in places">
      <h2>{{ place.name }}</h2>
      <p>{{ place.address }}</p>
      <button v-on:click="showPlace(place)">Edit Place</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Edit Place</h1>
        <p>Name: <input type="text" v-model="currentPlace.name"></p>
        <p>Address: <input type="text" v-model="currentPlace.address"></p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      places: [],
      name: "",
      address: "",
      currentPlace: {},
      errors: []
    };
  },
  created: function() {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function() {
      axios.get("/api/places").then(response => {
        this.places = response.data;
      });
    },
    createPlace: function() {
      var params = {
        name: this.name,
        address: this.address
      };
      axios
        .post("/api/places", params)
        .then(response => {
          this.places.push(response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
      this.name = "";
      this.address = "";
    },
    showPlace: function(place) {
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function(place) {
      var params = {
        name: place.name,
        address: place.address
      };
      axios.patch(`/api/places/${place.id}`, params);
    },
    destroyPlace: function(place) {
      axios.delete(`/api/places/${place.id}`);
      var index = this.places.indexOf(place);
      this.places.splice(index, 1);
    }
  }
};
</script>