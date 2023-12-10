<script setup>
  /** 
  完成以下功能：
  1. 將 movies 的內容用 v-for 改寫成動態渲染
  2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
  */
  import { ref } from "vue";
  import { StarIcon } from "@heroicons/vue/24/solid";
  import { items } from "./movies.json";

  const movies = ref(items);    //接收電影資料
  const starHover = ref(0);     //hovered star 數量

  const mouseover = ((star) => {
    starHover.value = star;
  })
  const mouseout = (() => {
    starHover.value = 0;
  })
  const changeRating = ((moviesIndex) => {
    console.log('starHover', starHover.value);
    console.log('moviesIndex',moviesIndex -1);
    console.log(movies.value[moviesIndex - 1].rating);
    movies.value[moviesIndex - 1].rating = starHover.value;
  })
</script>

<template>
<div class="app">
  <!--v-for?star狀態-->
  <div class="movie-list">
    <div class="movie-item" v-for="(movie) in movies" :key="movie.id">
      <div class="movie-item-image-wrapper">
        <img :src="movie.image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">{{movie.name}}</h3>
          <div class="movie-item-genres-wrapper">
            <template v-for="genre in movie.genres" :key="genre">
              <span class="movie-item-genre-tag" >{{genre}}</span>
            </template>
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description">{{movie.description}}</p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> Rating: ({{movie.rating}}/5) </span>
          
          <div class="movie-item-star-icon-wrapper">
              <button class="movie-item-star-icon-button" 
                      v-for="(star) in 5" 
                      :key="star" 
                      :class="[
                        star <= movie.rating ? 'text-yellow-500' : 'text-gray-500', 

                        star <= starHover ? 'text-yellow-500' : 'text-gray-500'
                      ]" 
                      @click="changeRating(movie.id, star)"

                      @mouseover="mouseover(star)"
                      @mouseout="mouseout()">
                <StarIcon class="movie-item-star-icon"/>
              </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
