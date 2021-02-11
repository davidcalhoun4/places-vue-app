<template>
  <div class="home">



    <div>
      Name: <input type="text" v-model="newName" /><br/>
      Address: <input type="text" v-model="newAddress" /><br/>
      Image Url: <input type="text" v-model="newImage" /><br/>
      <button v-on:click="createPlace()">Create Place</button>
    </div>

    <h1>{{ message }}</h1>

    <div v-for="place in places" v-bind:key="place.id">
      <h3> <u> {{ place.name }} </u> </h3>
      <img v-bind:src="place.image">
      <p> <strong> Adress: </strong> {{ place.address }} </p>
      <button v-on:click="showPlace(place)"> More Info </button>
    </div>

    <dialog>
      <form method="dialog">
        <h2>Place Info</h2>
        <img :src="currentPlace.image" alt="" />
        <p>Name: <input type="text" v-model="currentPlace.name" /> </p>
        <p>Adress: <input type="text" v-model="currentPlace.price" /></p>
        <p>Image Url: <input type="text" v-model="currentPlace.image_url" /> </p>
        <button v-on:click="updatePlace({ currentPlace })">Update</button><br/>
        <button v-on:click="destroyPlace(currentPlace)"> Delete </button><br/>
        <button>Close</button>
      </form>
    </dialog>

  </div>
</template>

<style>
img {
  width: 300px;
  height: 200px;
  border: 2px solid #fff;
  -moz-box-shadow: 10px 10px 5px #ccc;
  -webkit-box-shadow: 10px 10px 5px #ccc;
  box-shadow: 10px 10px 5px rgb(204, 204, 204);
  -moz-border-radius: 25px;
  -webkit-border-radius: 25px;
  border-radius: 25px;
}
h1 {
  color: rgba(0, 0, 0, 0.618);
  text-shadow: 2px 2px 4px #000000;
}
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Dance Centers in San Francisco, CA:",
      places: [],
      newName: "",
      newAddress: "",
      newImage: "",
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/api/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      var params = {
        name: this.newName,
        address: this.newAddress,
        image_url: this.newImage,
      };
      axios
        .post("/api/places", params)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("dialog").showModal();
    },
    updatePlace: function () {
      var params = {
        name: this.currentPlace.name,
        address: this.currentPlace.address,
        image_url: this.currentPlace.image_url,
      };
      axios
        .patch(`/api/places/${this.currentPlace.id}`, params)
        .then((response) => {
          console.log("successful update", response.data);
        })
        .catch((error) => {
          console.log("render and error", error.response.data);
        });
    },
    destroyPlace: function (inputPlace) {
      axios.delete(`/api/places/${inputPlace.id}`).then((response) => {
        console.log("success", response.data);
        var index = this.place.indexOf(inputPlace);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
