<template>
<loading v-if="$fetchState.pending"/>
  <div v-else class="single-movie container">
    <NuxtLink class="button" :to="{ name: 'index' }"> Back </NuxtLink>
    <div class="movie-info">
      <div class="movie-img">
        <img
          :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
          alt=""
        />
      </div>
      <div class="movie-content">
        <h1>Title: {{ movie.title }}</h1>
        <p class="movie-fact tagline">
          <span>Tagline:</span> "{{ movie.tagline }}"
        </p>
        <p class="movie-fact">
          <span>Released:</span>
          {{
            new Date(movie.release_date).toLocaleString('en-us', {
              month: 'long',
              day: 'numeric',
              year: 'numeric',
            })
          }}
        </p>
        <p class="movie-fact">
          <span>Duration:</span> {{ movie.runtime }} minutes
        </p>
        <p class="movie-fact">
          <span>Revenue:</span>
          {{
            movie.revenue.toLocaleString('en-us', {
              style: 'currency',
              currency: 'USD',
            })
          }}
        </p>
        <p class="movie-fact"><span>Overview:</span> {{ movie.overview }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Loading from '../../components/Loading.vue'
export default {
  components: { Loading },
  async fetch(){
    await this.getOneMovie()
  },
  fetchDelay: 1000,
  head(){
    return {
      title: this.movie.title
    }
  },
  data(){
    return{
      movie: ''
    }
  },
  methods: {
    async getOneMovie(){
      const data = axios.get(`https://api.themoviedb.org/3/movie/${this.$route.params.id}?api_key=e56153d24e892bba8fcb98f2f2f0d4d2&language=en-US`)
      const result = await data
      this.movie = result.data
    }
  }
}
</script>

<style lang="scss" scoped>
.single-movie{
    color: #fff;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 32px 16px;

    .button {
      align-self: flex-start;
      margin-bottom: 32px;
      margin-left: 40px;
    }

    .movie-info{
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 32px;
      color: #fff;
      @media (min-width: 800px) {
        flex-direction: row;
        align-items: flex-start;
      }

      .movie-img{
        img{
          max-height: 400px;
          width: 100%;

          @media (min-width: 800px) {
            margin-left: 40px;
            max-height: 500px;
            width: initial;
          }
        }
      }

      .movie-content{
        h1{
          font-size: 56px;
          font-weight: 400;
        }

        .movie-fact{
          margin-top: 12px;
          font-size: 20px;
          line-height: 1.5;

          span{
            padding-right: 20px;
            font-size: 25px;
            font-weight: 600;
            text-decoration: underline;
          }
        }

        .tagline{
          font-style: italic;

          span{
            font-style: normal;
          }
        }
      }
    }
  }
</style>