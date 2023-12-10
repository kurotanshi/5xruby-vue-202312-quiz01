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
      <div class="movie-item"
        v-for="movie in items" 
        :key="movie.id">
        <div class="movie-item-image-wrapper">
          <img :src="movie.image" class="movie-item-image">
        </div>
        <div class="movie-item-content-wrapper">
          <div class="movie-item-title-wrapper">
            <h3 class="movie-item-title">{{ movie.name }}</h3>
            <div class="movie-item-genres-wrapper">
              <span class="movie-item-genre-tag" 
                v-for="genre in movie.genres" 
                :key="genre">{{ genre }}</span>
            </div>
          </div>
          <div class="movie-item-description-wrapper">
            <p class="movie-item-description">{{ movie.description }}</p>
          </div>
          <div class="movie-item-rating-wrapper">
            <span class="movie-item-rating-text"> Rating: ({{movie.rating}}/5) </span>
            <div class="movie-item-star-icon-wrapper">
              <button class="movie-item-star-icon-button" v-for="i in 5">
                <StarIcon 
                  :class="{
                    'text-yellow-500': i <= movie.rating, 
                    'text-gray-500': i > movie.rating
                  }"
                  class="movie-item-star-icon" />
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>