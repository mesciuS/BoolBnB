<script>

import { store } from '../store.js';

export default {
    name: 'ServiceHouses',

    data() {
        return {
           store 
        }
    },

    props: {
        house: Object,
    },

  
}
</script>

<template>
    
    <div class="my_card">
      <img :src="'http://127.0.0.1:8000/storage/' + house.thumbnail"  alt="Immagine di copertina">
       
      <div class="my_card_text">
             <strong>{{house.title}}</strong>
             <em>{{ house.street}} , {{house.house_number}} - {{house.city}}</em>

             <div>
               <span v-for="service in house.services">
                     <i :class="service.icon" class="p-2"></i>
               </span>
             </div>

             <div v-if="house.distance != null && house.distance > 0">
               <span>{{ house.distance }} km di distanza.</span>
             </div>
             <div v-else-if="house.distance != null && house.distance == 0">
               <span>Meno di 1 km di distanza.</span>
             </div>

      </div>
       
      <div class="my_show_btn">
             <router-link :to="{name:'show', params:{id: house.id}}" class="btn">
               <span >Info</span>
               <svg width="34" height="34" viewBox="0 0 74 74" fill="none" xmlns="http://www.w3.org/2000/svg">
               <circle cx="37" cy="37" r="35.5" stroke="black" stroke-width="3"></circle>
               <path d="M25 35.5C24.1716 35.5 23.5 36.1716 23.5 37C23.5 37.8284 24.1716 38.5 25 38.5V35.5ZM49.0607 38.0607C49.6464 37.4749 49.6464 36.5251 49.0607 35.9393L39.5147 26.3934C38.9289 25.8076 37.9792 25.8076 37.3934 26.3934C36.8076 26.9792 36.8076 27.9289 37.3934 28.5147L45.8787 37L37.3934 45.4853C36.8076 46.0711 36.8076 47.0208 37.3934 47.6066C37.9792 48.1924 38.9289 48.1924 39.5147 47.6066L49.0607 38.0607ZM25 38.5L48 38.5V35.5L25 35.5V38.5Z" fill="black"></path>
               </svg>
             </router-link>
       
      </div>
  
    </div>
  </template>

<style scoped lang="scss">


@import "../scss/variables";
@import "../scss/mixins";


.my_card{
  display: flex;
  flex-wrap: wrap;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  min-height: 500px;

  .my_card_text{
    display: flex;
    flex-direction: column;
    text-align: center;
  }

  img{
    width: 100%;
    aspect-ratio: 4/3;

    @include border();

    
    transition: ease-in-out 1s;

    &:hover{
      transform: scale(1.04);
    }
  }

  .my_show_btn{
   display: flex;
   justify-content: center;
     .btn {
       cursor: pointer;
       font-weight: 700;
       letter-spacing: 3px;
        transition: all .2s;
       padding: 10px 20px;
       border-radius: 100px;
       background: $accent;
       color: white;
       border: 1px solid transparent;
       display: flex;
       align-items: center;
      font-size: 15px;
    }


    .btn:hover {
       background: $secondary;
       color: $text;
     }

     .btn > svg {
       width: 34px;
       margin-left: 10px;
       transition: transform .3s ease-in-out;
     }

     .btn:hover svg {
       transform: translateX(5px);
     }

     .btn:active {

      transform: scale(0.95);
    }
  
  }

}
</style>