<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h2>Add a new place</h2>
    <ul>
      <li v-for="error in errors">{{error}}</li>
    </ul>

    <div>
      Name: <input type="text" v-model="newPlaceName"> <br>
      Address: <input type="text" v-model="newPlaceAddress"> <br>
      <button v-on:click="createPlace()">Create Place</button>
    </div>

    <div v-for="place in places">
      <h2> {{place.name}}</h2>
      <p> {{place.address}} </p>
      <button v-on:click="showInfo(place)">Show more info</button>
    </div>

    <dialog id="place-details">
      <form method="dialog">
        <h2>Place Details</h2>
        <p>Name: <input type="text" v-model="currentPlace.name"> </p>
        <p>Address: <input type="text" v-model="currentPlace.address"> </p>
        <div>
          <button v-on:click="updatePlace(currentPlace)">Update</button>
          <button v-on:click="destroyPlace(currentPlace)">Delete</button>
          <button>Close</button>
        </div>
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
      message: "Your Places!",
      places: [],
      errors: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {}
    };
  },
  created: function() {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function() {
      axios.get("/api/places").then(response => {
        console.log("All Places:", response.data);
        this.places = response.data;
      });
    },
    createPlace: function() {
      var params = {
        name: this.newPlaceName,
        description: this.newPlaceAddress
      };
      axios
        .post("/api/places", params)
        .then(response => {
          console.log("New Place:", response.data);
          this.places.push(response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showInfo: function(place) {
      this.currentPlace = place;
      console.log(place);
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function(place) {
      var params = {
        name: place.name,
        address: place.address
      };
      axios.patch(`/api/places/${place.id}`, params).then(response => {
        console.log("Updated:", response.data);
      });
    },
    destroyPlace: function(place) {
      axios.delete(`/api/places/${place.id}`).then(response => {
        console.log("Deleted:", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    }
  }
};
</script>
