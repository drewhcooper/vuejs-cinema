<template>
  <div id="movie-list">
    <div v-if="filteredMovies.length">
      <movie-item v-for="movie in filteredMovies"
            v-bind:movie = "movie.movie"
            v-bind:sessions="movie.sessions"
            v-bind:day = "day">
      </movie-item>
    </div>
    <div v-else-if="movies.length">
      No results.
    </div>
    <div v-else>
      Loading...
    </div>
  </div>
</template>
<script>
  import genres from '../util/genres';
  import times from '../util/times';
  import MovieItem from './MovieItem.vue';

  export default {
      methods: {
        moviePassesGenreFilter(movie) {
          let matched = true;
          if (!this.genre.length) {
            return true;
          } else {
            let movieGenres = movie.movie.Genre.split(", ");
            this.genre.forEach(genre => {
              if (movieGenres.indexOf(genre) === -1) {
                matched = false;
              }
            });
          }
          return matched;
        },
        sessionPassesTimeFilter(session) {
          if (!this.day.isSame(this.$moment(session.time), 'day')) {
            return false;
          }
          else if (this.time.length === 0 || this.time.length === 2) {
            return true;
          }
          else if (this.time[0] === times.AFTER_6PM) {
            return this.$moment(session.time).hour() >= 18;
          } else {
            return this.$moment(session.time).hour() < 18;
          }
        }
      },
      computed: {
        filteredMovies() {
          return this.movies
            .filter(this.moviePassesGenreFilter)
            .filter(movie => movie.sessions.find(this.sessionPassesTimeFilter));
            ;
        }
      },
      props: ['genre', 'time', 'movies', 'day'],
      components: {
        MovieItem
      }
  }
</script>
