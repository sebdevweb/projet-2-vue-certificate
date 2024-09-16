<script setup>
  import { ref } from 'vue'
  import { StarIcon } from '@heroicons/vue/24/solid'
  import { items } from './movies.json'
  
  const text = ref('MOVIE RATING APP')
  const movies = ref(items)
</script>


<template>
  <h1>{{ text }}</h1>
  
  <div class="movies">
    <!-- Card Movie -->
    <div class="card" v-for="movie in movies" :key="movie.id">
      <div class="card_pic">
        <img :src="movie.image" :alt="movie.name">
      </div>
      <div class="card_info">
        <h2 class="card_info--title">{{ movie.name }}</h2>
        <div class="card_info--captions">
          <p class="caption" v-for="genre in movie.genres">{{ genre }}</p>
        </div>
        <p class="card_info--description">{{ movie.description }}</p>
        <div class="card_info--actors">
          <p class="actor" v-for="actor in movie.actors">{{ actor }}</p>
        </div>
        <div class="card_info--rating">
          <p>Rating [{{ movie.rating }}/5]</p>
          <div class="stars">
            <button 
              type="button" 
              v-for="star in 5" 
              :key="star"
              :class="star <= movie.rating ? 'gold' : 'grey'"
              >
              <StarIcon />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
  
</template>


<style lang="scss">
  body {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: sans-serif;
  }
  
  h1 {
    text-align: center;
    color: #5A94E2;
    border: #5A94E2 3px dashed;
    max-width: 400px;
    margin: 24px auto;
    padding: 12px;
  }

  .movies {
    display: flex;
    gap: 16px;
    justify-content: space-around;
    background-color: #42b683;
    padding: 48px;
    height: auto;
    flex-wrap: wrap;
    max-width: 1600px;
    margin: 0 auto;
    border-radius: 20px;
    margin-bottom: 48px;

    .card {
      max-width: 400px;
      height: auto;
      border-radius: 10px;
      overflow: hidden;
      box-shadow: 0px 0px 30px rgba(0, 0, 0, 0.3);
      &_pic {
        width: 400px;
        height: 560px;
        overflow: hidden;
        img {
          max-width: 400px;
          height: auto;
        }
      }
      &_info {
        padding: 20px;
        height: 330px;
        background-color: #fff;
        position: relative;
        &--title {
          margin: 0 0 8px 0;
          line-height: 1;
          font-size: 22px;
        }
        &--captions {
          display: flex;
          gap: 8px;
          .caption {
            background-color: #5A94E2;
            padding: 4px 8px;
            border-radius: 5px;
            color: #fff;
          }
        }
        &--description {
          color: #333;
          line-height: 1.5;
        }
        &--actors {
          display: flex;
          flex-wrap: wrap;
          word-break: normal;
          word-spacing: -2px;
          gap: 10px;
          .actor {
            color: #5A94E2;
            line-height: .5;
            margin: 0;
          }
        }
        &--rating {
          display: flex;
          align-items: center;
          justify-content: space-between;
          position: absolute;
          left: 20px;
          bottom: 20px;
          .stars {
            margin-left: 10px;
            gap: 5px;
            button {
              cursor: pointer;
              border: none;
              background: none;
              padding: 2px;
              &.gold {
                color: rgb(213, 180, 16);
              }
              &.grey {
                color: lightgray;
              }
              svg {
                width: 20px;
              }
            }
          }
        }
      }
    }
  }
</style>
