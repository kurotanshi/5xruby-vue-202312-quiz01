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
const _ratingTotal = 5;
let _temp = {};

movies.value.forEach((item, index)=>{
  _temp[index] = item.rating;
});

function clickRating(oIndex, oRIndex, oItem) {
  oItem.rating = oRIndex;
  _temp[oIndex] = oItem.rating;
};

function mouseInRating(oRIndex, oItem){
  oItem.rating = oRIndex;
};

function mouseOutRating(oIndex, oItem){
  oItem.rating = _temp[oIndex];
};

</script>

<template>
<div class="app">
  <div class="movie-list">

    <div class="movie-item" v-for="(item, index) in movies" :key="item.id">
      <div class="movie-item-image-wrapper">
        <img :src="item.image" class="movie-item-image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">{{item.name}}</h3>
          <div class="movie-item-genres-wrapper">
            <span class="movie-item-genre-tag" v-for="genre in item.genres" :key="item.id">{{ genre }}</span>
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description">{{ item.description }}</p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> Rating: ({{item.rating}}/{{_ratingTotal}}) </span>
          
          <div class="movie-item-star-icon-wrapper">
            <button v-for="rIndex in _ratingTotal" :key="item.id"
            :class="{
              'text-yellow-500': item.rating >= rIndex,
              'text-gray-500': item.rating < rIndex,
            }" class="movie-item-star-icon-button" 
            @click="clickRating(index, rIndex, item)"
            @mouseover="mouseInRating(rIndex, item)"
            @mouseleave="mouseOutRating(index, item)">
              <StarIcon class="movie-item-star-icon" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
