<script>
import axios from "axios";
export default {
  data: function () {
    return {
      movies: [],
      newMovie: {},
      currentMovie: {},
      errorMessage: "",
    };
  },
  created: function () {
    this.indexMovies();
  },
  methods: {
    indexMovies: function () {
      axios.get("http://localhost:3000/movies").then((response) => {
        this.movies = response.data;
        console.log(this.movies);
      });
    },
    createMovie: function () {
      axios
        .post("http://localhost:3000/movies", this.newMovie)
        .then((response) => {
          this.movies.push(response.data);
          console.log(this.newMovie);
        })
        .catch((error) => (this.errorMessage = error.response.data.errors));
      this.newMovie = {};
    },
    showMovie: function (movie) {
      this.currentMovie = movie;
      document.querySelector("#show-movie").showModal();
    },
    updateMovie: function (movie) {
      axios
        .patch("http://localhost:3000/movies/" + movie.id, movie)
        .then((response) => {
        console.log(response);
        })
        .catch((error) => console.log(error.response));
    },
    deleteMovie: function (movie) {
      axios.delete(`http://localhost:3000/movies/${movie.id}`).then((response) => console.log(response));
      this.movies.splice(this.movies.indexOf(movie, 1));
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>Movie Frontend - Take 2!</h1>
    <div>
      <h3>Make a Movie!</h3>
      <p>
        Title:
        <input type="text" v-model="newMovie.title" />
      </p>
      <p>
        Director:
        <input type="text" v-model="newMovie.director" />
      </p>
      <p>
        Year:
        <input type="text" v-model="newMovie.year" />
      </p>
      <p>
        Plot:
        <input type="text" v-model="newMovie.plot" />
      </p>
      <button v-on:click="createMovie()">CREATE MOVIE</button>
      <div id="red" v-if="errorMessage">
        <div v-for="error in errorMessage" v-bind:key="error">
          <p>{{ error }}</p>
        </div>
      </div>
    </div>

    <br />
    <div v-for="movie in movies" v-bind:key="movie.id">
      <h3>{{ movie.title }}</h3>
      <button v-on:click="showMovie(movie)">More Info</button>
    </div>
    <dialog id="show-movie">
      <form method="dialog">
        <h2>{{ currentMovie.title }}</h2>
        <p>{{ currentMovie.director }}</p>
        <p>{{ currentMovie.year }}</p>
        <p>{{ currentMovie.plot }}</p>
        <h3>Edit Movie:</h3>
        <p>
          Title:
          <input type="text" v-model="currentMovie.title" />
        </p>
        <p>
          Director:
          <input type="text" v-model="currentMovie.director" />
        </p>
        <p>
          Year:
          <input type="text" v-model="currentMovie.year" />
        </p>
        <p>
          Plot:
          <input type="text" v-model="currentMovie.plot" />
        </p>
        <button v-on:click="updateMovie(currentMovie)">SAVE MOVIE INFO</button>
        <br />
        <button v-on:click="deleteMovie(currentMovie)" id="red">DELETE MOVIE</button>
        <br />
        <button>CLOSE</button>
      </form>
    </dialog>
  </div>
</template>

<style>
#red {
  color: red;
}
</style>
