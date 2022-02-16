<template>
  <div class="home">
    <!-- Hero -->
    <Hero />

    <!-- Search -->
    <div class="container search">
      <input v-model.lazy="searchInput" type="text" placeholder="Seach" @keyup.enter="$fetch">
      <button v-show="searchInput !== ''" class="button" @click="clearSearch">Clear Search</button>
    </div>

    <!-- Loading -->
    <Loading v-if="$fetchState.pending" />

    <!-- Movies -->
    <div v-else class="container movies">
      <div v-if="searchInput !== ''" id="movie-grid" class="movies-grid">
        <Movie v-for="(movie, index) in searchedMovies" :key="index" :movie="movie" />
      </div>

      <div v-else id="movie-grid" class="movies-grid">
        <Movie v-for="(movie, index) in movies" :key="index" :movie="movie" />
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import apiKey from '../apiKey'

export default {
  data() {
    return {
      movies: [],
      searchedMovies: [],
      searchInput: ''
    }
  },
  async fetch() {
    if (this.searchInput === '') {
      await this.getMovies()
      return
    }

    await this.searchMovies()
  },
  head() {
    return {
      title: 'Nuxt Movie App - Lastest movie info',
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: 'Get all the latest movies in theaters & online'
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: 'movies, stream, streaming'
        },
      ]
    }
  },
  methods: {
    async getMovies() {
      const data = axios.get(
        `https://api.themoviedb.org/3/movie/now_playing?api_key=${apiKey}&language=en-GB&page=1&region=GB`
      )
      const result = await data
      result.data.results.forEach(movie => {
        this.movies.push(movie)
      });
    },
    async searchMovies() {
      const data = axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=${apiKey}&language=en-GB&page=1&query=${this.searchInput}`
      )
      const result = await data
      result.data.results.forEach(movie => {
        this.searchedMovies.push(movie)
      });
    },
    clearSearch() {
      this.searchInput = ''
      this.searchedMovies = []
    }
  }
}
</script>

<style lang="scss" scoped>
.home {
  .loading {
    padding-top: 120px;
    align-items: flex-start;
  }
  .search {
    display: flex;
    padding: 32px 16px;
    input {
      max-width: 350px;
      width: 100%;
      padding: 12px 6px;
      font-size: 14px;
      border: none;
      &:focus {
        outline: none;
      }
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
    }
  }
  .movies {
    padding: 32px 16px;
    .movies-grid {
      display: grid;
      column-gap: 32px;
      row-gap: 64px;
      grid-template-columns: 1fr;
      @media (min-width: 500px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 750px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 1100px) {
        grid-template-columns: repeat(4, 1fr);
      }
    }
  }
}
</style>