<script setup>
/** 
完成以下功能：
1. 將 movies 的內容用 v-for 改寫成動態渲染
2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
*/
import { ref, computed } from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";

const movies = ref(items);
// const starRating = ref(0);
// console.log(movies.value[2].rating);


const toggleStar = (movie, stars) => {
  movie.rating = stars;
 // for (let i = 0; i < movies.value.length; i++) {
   // if (movies.value[i].id === id) {
    //    movies.value[i].rating = stars;
        // console.log(movies.value[i].id);
        // console.log(movies.value[i].rating);
    //  }
   // }
}



</script>

<template>
<div class="app">
  <div class="movie-list">
    <!-- 電影清單 -->
    <div v-for="movie in movies" :key="movie.id" class="movie-item">
      <div class="movie-item-image-wrapper">
        <img :src="movie.image" class="movie-item-image" :alt="`${movie.name} - image`">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">{{movie.name}}</h3>
          <div class="movie-item-genres-wrapper">
            <span  v-for="tag in movie.genres"  class="movie-item-genre-tag">{{tag}}</span>
            <!-- <span class="movie-item-genre-tag">{{tag}}</span> -->
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description">{{ movie.description }}</p>
        </div>
        <!-- 5星評分 -->
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> Rating: ({{movie.rating}}/5) </span>
          
          <div class="movie-item-star-icon-wrapper">
            <button v-for="star in 5" :key="star" @click="toggleStar(movie, star)" class=" movie-item-star-icon-button">
              <StarIcon :class="movie.rating >= star ? 'text-yellow-500' : 'text-gray-500'" class="movie-item-star-icon" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
