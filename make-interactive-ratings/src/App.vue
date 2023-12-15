<script setup>
/** 
完成以下功能：
1. 將 movies 的內容用 v-for 改寫成動態渲染
2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
*/
import { ref } from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";

const movies = ref(items);
</script>

<template>
<div class="app">
  <div class="movie-list">
    <div class="movie-item" v-for=" movie in movies" >
      <div class="movie-item-image-wrapper">
        <img :src="movie.image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">{{ movie.name }}</h3>
          <div class="movie-item-genres-wrapper" >
            <span class="movie-item-genre-tag" v-for=" genre in movie.genres">{{ genre }}</span>
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description" >{{ movie.description }}</p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> {{ `Rating (${movie.rating}/5)` }} </span>
          
          <div class="movie-item-star-icon-wrapper">
            <button class="movie-item-star-icon-button"
                    v-for="star in 5"
                    v-bind:class="star <= movie.rating ? 'text-yellow-500' : 'text-gray-500'"
                    @click="movie.rating = star">
              <StarIcon class="movie-item-star-icon" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
