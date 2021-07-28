<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <div> 

      <h2> You can add a new movie to your favorites, it better be good. </h2>
     <p>  Name: <input type="text" v-model="newMovieParams.title">  </p> 
     <p>  Year: <input type="text" v-model="newMovieParams.year">  </p> 
      <p>  Plot: <input type="text" v-model="newMovieParams.plot">  </p>
      <p>  Director: <input type="text" v-model="newMovieParams.director">  </p> 
      <button v-on:click="createMovie();"> Add </button> 
    </div>

    <h2> All the movies </h2>
    <div v-for="movie in movies"> 
      <p> {{ movie.id}}</p>
      <p> {{ movie.title}}</p>
      <p> {{ movie.year}}</p>
      <p> {{ movie.plot}}</p>
      <p> {{ movie.director}}</p>
      <button v-on:click="showMovie(movie);">  Show more Info about this movie </button>

      <hr> 

    </div>

    <dialog id="movie-details">  
      <form method= "dialog">
        <h2> Movie Information </h2>
        <p> Title: <input type="text" v-model="currentMovie.title"> </p>
        <p> Year: <input type="text" v-model="currentMovie.year"> </p>
        <p> Plot: <input type="text" v-model="currentMovie.plot"> </p>
        <p> Director: <input type="text" v-model="currentMovie.director"> </p>
       
        <button v-on:click ="updateMovie(currentMovie)"> Edit </button>
        <button v-on:click ="deleteMovie(currentMovie)"> Delete </button>
        <button> Close </button>
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
      message: "Welcome to the Movies App",
      movies: [],
      newMovieParams: {},
      currentMovie: {},
      editMovieParams: {},
    };
  },
  created: function () {
    this.indexMovie();
  },
  methods: {
    indexMovie: function () {
      console.log("showing index...");
      axios.get("http://localhost:3000/movies").then((response) => {
        console.log(response.data);
        this.movies = response.data;
      });
    },
    createMovie: function () {
      console.log("making a new movie");
      axios.post("/movies", this.newMovieParams).then((response) => {
        console.log(response.data);
        this.movies.push(response.data);
        this.newMovieParams = {};
      });
    },
    showMovie: function (movie) {
      console.log("in the show ");
      this.currentMovie = movie;
      document.querySelector("#movie-details").showModal();
    },
    updateMovie: function (movie) {
      var editMovieParams = movie;

      axios
        .patch("http://localhost:3000/movies/" + movie.id, editMovieParams)
        .then((response) => {
          console.log(response.data);
          this.currentMovie = {};
        })
        .catch((error) => {
          console.log("Movie update error", error.response);
        });
    },
    deleteMovie: function (movie) {
      axios
        .delete("http://localhost:3000/movies/" + movie.id)
        .then((response) => {
          console.log(response.data);
          var index = this.movies.indexOf(movie);
          this.movies.splice(index, 1);
        });
    },
  },
};
</script>

