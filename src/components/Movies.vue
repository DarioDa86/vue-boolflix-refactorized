<template>
  <div class="movies">
    <div class="fluid-container" v-if="displayMoviesSection">
      <div class="header">
        <h2>Movies</h2>
        <select v-model="currentGenre">
          <option :value="null" selected>All</option>
          <option v-for="genre in genres" :key="genre.id" :value="genre.id">
            {{ genre.name }}
          </option>
        </select>
      </div>
      <div class="row row-cols-1 row-cols-md-2 row-cols-lg-4 row-cols-xl-5">
        <div class="col" v-for="movie in filteredMovies" :key="movie.id">
          <Movie :movie="movie" @playVideo="playVideo" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Movie from "./Movie.vue";
export default {
  name: "Movies",
  props: ["query"],
  components: {
    Movie,
  },
  data() {
    return {
      displayMoviesSection: false,
      movies: [],
      genres: [],
      currentGenre: null,
    };
  },
  watch: {
    query() {
      this.getMovies(this.query);
    },
  },
  created() {
    this.getMovies();
  },
  methods: {
    getMovies(query) {
      const url =
        query != null && query != ""
          ? "https://api.themoviedb.org/3/search/movie"
          : "https://api.themoviedb.org/3/movie/popular";
      const params = {
        api_key: "af0ba66c25483bbc937edba39186698d",
        language: "it-IT",
      };
      if (query != null && query != "") params.query = query;
      axios.get(url, { params }).then((res) => {
        // console.log(res.data.results);
        this.movies = res.data.results;
        this.getDisplayMoviesSection();
      });
    },
    getDisplayMoviesSection() {
      this.displayMoviesSection = this.movies.length != 0;
      axios
        .get("https://api.themoviedb.org/3/genre/movie/list", {
          params: {
            api_key: "af0ba66c25483bbc937edba39186698d",
            language: "it-IT",
          },
        })
        .then((res) => {
          // console.log(res.data.genres);
          this.genres = res.data.genres;
        });
    },
    playVideo(trailer) {
      this.$emit("playVideo", trailer);
    },
  },
  computed: {
    filteredMovies() {
      const filter = this.movies.filter((movie) => {
        // console.log(this.currentGenre);
        if (this.currentGenre == null) return true;
        return movie.genre_ids.includes(this.currentGenre);
      });
      return filter;
    },
  },
};
</script>

<style scoped lang="scss">
.movies {
  .fluid-container {
    .header {
      display: flex;
      justify-content: space-between;
      padding: 0.5em;
      color: lightgray;
      background: linear-gradient(180deg, rgba(0,0,0,1) 1%, rgba(20,20,20,1) 50%, rgba(0,0,0,1) 99%);
    }
    .row {
      > .col {
        padding: 1em;
      }
    }
  }
}
</style>