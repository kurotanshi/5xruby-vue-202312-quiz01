<script setup>
  /** 
  完成以下功能：
  1. 將 movies 的內容用 v-for 改寫成動態渲染
  2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
  */
  import { ref } from "vue";
  import { StarIcon } from "@heroicons/vue/24/solid";
  import { items } from "./movies.json";

  const movies = ref(items);                                        //電影正本
  
  const copyMovies = ref(JSON.parse(JSON.stringify(movies.value))); //電影副本
  console.log(copyMovies.value[0]); 

  copyMovies.value.forEach(element => {                             //電影副本添加屬性starHover
    element.starHover = element.rating; 
    //Kuro:是用來表示亮燈與否，跟實際的分數不相關
    //所以這裡的預設值一開始就可以讓他吃 rating 而不是零
    console.log('foreach element', element.starHover);
  });

  // const mouseover = ((moviesIndex, star) => {                       //hover: Kuro:這兩個就用不到了
  //   console.log('[hover]星星數',star);
  //   console.log('[hover]電影資料 array index', moviesIndex - 1);
  //   copyMovies.value[moviesIndex - 1].starHover = star;
  //   console.log("[hover]星星顯示數", copyMovies.value[moviesIndex - 1].starHover);
  // })
  // const mouseout = ((moviesIndex) => {                              //unHover: Kuro:這兩個就用不到了
  //   copyMovies.value[0].starHover = 0;
  // })
  const changeRating = ((moviesIndex, star) => {
    console.log('[點擊]星星數', star);
    console.log('[點擊]電影資料 array index',moviesIndex -1);
    copyMovies.value[moviesIndex - 1].rating = star;
    console.log('[點擊]電影評分',movies.value[moviesIndex - 1].rating);
  })
</script>

<template>
<div class="app">
  <!--v-for?star狀態-->
  <div class="movie-list">
    <div class="movie-item" v-for="(movie) in copyMovies" :key="movie.id">
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

                        star <= movie.starHover ? 'text-yellow-500' : 'text-gray-500'
                      ]" 
                      @click="changeRating(movie.id, star)"
                      @mouseover="mouseover(movie.id, star)"
                      @mouseout="mouseout(movie.id)">
                <StarIcon class="movie-item-star-icon"/>
              </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
