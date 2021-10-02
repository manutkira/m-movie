<template>
  <div class="home">

    <!-- hero -->
    <hero/>

    <!-- search -->
    <div class="container search">
      <input type="text" placeholder="Search" v-model.lazy="searchInput" @keyup.enter="$fetch">
      <button @click="clearSearch" class="button" v-show="searchInput !== ''">Clear Search</button>
    </div>

    <!-- loading -->
    <loading v-if="$fetchState.pending"/>

    <!-- movies -->
    <div v-else class="container movies">

      <!-- searched movie -->
      <div v-if="searchInput !== ''" id="movie-grid" class="movies-grid">
        <div class="movie" v-for="(movie, index) in searchedMovie" :key="index">
          <div class="movie-img">
            <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="">
            <p class="review">{{movie.vote_average}}</p>
            <p class="overview">{{movie.overview}}</p>
          </div>
          <div class="info">
            <p class="title">{{movie.title.slice(0,25)}}<span v-if="movie.title.length > 25">...</span></p>
            <p class="release">Language: English</p>
            <p class="release">Released: {{new Date(movie.release_date).toLocaleString('en-us', {
              month: 'long',
              day: 'numeric',
              year: 'numeric',
              })}}</p>
              <nuxt-link :to="{name: 'movies-id', params: {id: movie.id}}" class="button button-light">Get more info</nuxt-link>
          </div>
        </div>
      </div>

      <!-- not search -->
      <div v-else id="movie-grid" class="movies-grid">
        <div class="movie" v-for="(movie, index) in movies" :key="index">
          <div class="movie-img">
            <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="">
            <p class="review">{{movie.vote_average}}</p>
            <p class="overview">{{movie.overview}}</p>
          </div>
          <div class="info">
            <p class="title">{{movie.title.slice(0,25)}}<span v-if="movie.title.length > 25">...</span></p>
            <p class="release">Language: English</p>
            <p class="release">Released: {{new Date(movie.release_date).toLocaleString('en-us', {
              month: 'long',
              day: 'numeric',
              year: 'numeric',
              })}}</p>
              <nuxt-link :to="{name: 'movies-id', params: {id: movie.id}}" class="button button-light">Get more info</nuxt-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Loading from '../components/Loading.vue'
export default {
  components: { Loading },
  head(){
    return {
      title: 'M-Movie'
    }
  },
  data(){
    return{
      movies: [],
      searchedMovie: [],
      searchInput: '',
    }
  },
  async fetch(){
    if(this.searchInput === ''){
      await this.getMovies()
      return
    }
    await this.searchMovie()
  },
  fetchDelay: 1000,
  methods: {
    async getMovies(){
      const data = axios.get('https://api.themoviedb.org/3/movie/now_playing?api_key=e56153d24e892bba8fcb98f2f2f0d4d2&language=en-US&page=1')
      const result = await data
      result.data.results.forEach(movie => {
        this.movies.push(movie)
      })
    },
    async searchMovie(){
      const data = axios.get(`https://api.themoviedb.org/3/search/movie?api_key=e56153d24e892bba8fcb98f2f2f0d4d2&language=en-US&page=1&query=${this.searchInput}`)

      const result = await data
      result.data.results.forEach(movie => {
        this.searchedMovie.push(movie)
      })
    },
    clearSearch(){
      this.searchedMovie = []
      this.searchInput = ''
    }
  }
}
</script>

<style lang="scss" scoped>
.loading{
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

    &:focus{
      outline: none;
    }
  }

  .button{
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
      .movie {
        position: relative;
        display: flex;
        flex-direction: column;
        .movie-img {
          position: relative;
          overflow: hidden;
          &:hover {
            img{
              cursor: pointer;
              filter: brightness(35%);
            }
            .overview {
              transform: translateY(0);
            }
          }
          img {
            display: block;
            width: 100%;
            height: 100%;
            border: 2px solid red;
            border-top-right-radius: 50px;
            border-bottom-left-radius: 50px;
          }
          .review {
            position: absolute;
            top: 0;
            left: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 40px;
            height: 40px;
            background-color: #c92502;
            color: #fff;
            border-radius: 0 0 16px 0;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
              0 2px 4px -1px rgba(0, 0, 0, 0.06);
          }
          .overview {
            line-height: 1.5;
            position: absolute;
            bottom: 0;
            background-color: rgb(255, 50, 128);
            padding: 12px;
            color: rgb(2, 2, 2);
            transform: translateY(100%);
            transition: 0.4s ease-in-out all;
          }
        }
        .info {
          margin-top: auto;
          .title {
            margin-top: 8px;
            color: #fff;
            font-size: 20px;
          }
          .release {
            margin-top: 8px;
            color: #c9c9c9;
          }
          .button {
            margin-top: 8px;
            border-radius: 50px;
            text-transform: uppercase;
            margin-top: 20px;
          }
        }
      }
    }
}

</style>