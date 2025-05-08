<script setup>
import HelloWorld from './components/HelloWorld.vue';

import { ref, onMounted, computed } from 'vue';
import axios from 'axios'

const products = ref([])
const currentIndex = ref(0)

const fetchProducts = async () => {
  try {
    const response = await axios.get('https://fakestoreapi.com/products');
    console.log(response);
    products.value = response.data;
  } catch (error) {
    console.log('Gagal', error)
  }
}

onMounted(fetchProducts)

const currentProduct = computed(() => {
  return products.value[currentIndex.value] || null;
})

const currentCategoryClass = computed(() => {
  if(!currentProduct.value) {
    return "bg-unavailable";
  }

  const category = currentProduct.value.category;
  if(category === "women's clothing"){
    return "bg-women"
  }

  if(category === "men's clothing"){
    return "bg-men"
  }

  // return 'bg-unavailable'
})

// color button
const buttonColor = computed(() => {
  if(!currentProduct.value){
    return "#000000"
  }

  const category = currentProduct.value.category;
  if(category === "women's clothing"){
    return "#720060"
  }

  if(category === "men's clothing"){
    return "#002772"
  }
})

const nextProduct = () => {
  if(currentIndex.value < products.value.length - 1) {
    currentIndex.value++
  } else {
    currentIndex.value = 0
  }
}

</script>

<template>
  <div :class="['wrapper', currentCategoryClass]">
    <div class="card" v-if="currentProduct"> 
      <div class="product-image">
        <img :src="currentProduct.image" alt="Product Image" />
      </div>
      <div class="content-section">
        <h2 class="title">{{ currentProduct.title }}</h2>
        <p class="category">{{ currentProduct.category }}</p>
        <div class="rating">2.9/5 <span class="stars">*****</span></div>
        <p class="description">{{ currentProduct.description }}</p>
        <p class="price">${{ currentProduct.price }}</p>
        <div class="buttons">
          <button :style="{ backgroundColor: buttonColor }">Beli Sekarang!</button>
          <button class="next" @click="nextProduct">Produk Selanjutnya</button>
        </div>
      </div>
    </div>

    <div class="card unavailable" v-else>
      <div class="sad-face">😭</div>
      <p class="text">Produk Ini Tidak Ada</p>
      <button class="next" @click="nextProduct">Produk Selanjutnya</button>
    </div>
  </div>
</template>

<style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');

  * {
    font-family: "Inter", sans-serif;
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    width: 100%;
    height: 100%;

  }

  .wrapper {
    min-height: 100vh;
    /* width: 100vw; */
    /* height: 100vw; */
    display: flex;
    align-items: center;
    justify-content: center;
    /* transition: background 0.3s ease ; */
    padding: 2rem;
    background: #fde2ff;
  }

  .bg-women {
    background: linear-gradient(to bottom, black, #ffffff);
  }

  .bg-men {
    background: linear-gradient(to bottom, #d6e6ff 60%, #ffffff);
  }

  .bg-unavailable {
    background: black;
  }

  .card {
    background: white;
    display: flex;
    overflow: hidden;
    /* position: fixed; */
    /* padding: 30px; */
    border-radius: 10px;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
    max-width: 800px;
    /* text-align: center; */
    min-height: 350px;
  }

  .product-image{
    flex: 1;
    padding: 1.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    /* max-height: 200px;
    object-fit: contain;
    margin-bottom: 15px; */
  }

  .product-image img {
    max-height: 250px;
    object-fit: contain;
  }

  .content-section {
    flex: 2;
    padding: 1.5rem;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .title {
    font-size: 1rem;
    font-weight: 600;
    color: #333;
  }

  .category {
    font-size: 0.9rem;
    color: #777;
    margin-top: 4px;
  }

  .rating {
    font-size: 0.9rem;
    color: #333;
    margin: 0.5rem 0;
  }

  .stars {
    color: #6633cc;
    margin-left: 5px;
  }

  .description {
    font-size: 0.9rem;
    /* margin: 10px 0; */
    line-height: 1.4;
    color: #444;
  }

  .price {
    font-size: 1.3rem;
    font-weight: bold;
    margin: 15px 0;
    color: black;
  }

  .buttons{
    display: flex;
    gap: 10px;
  }

  .buttons button {
    border: none;
    padding: 10px 18px;
    border-radius: 6px;
    color: white;
    font-weight: bold;
    cursor: pointer;
  }

  .next {
    background: white !important;
    color: black !important;
    border: 1px solid #aaa;
  }

  .unavailable {
    flex-direction: column;
    text-align: center;
    padding: 3rem;
  }

  .sad-face {
    font-size: 4rem;
    color: #aaa;
  }

  .text {
    margin: 1rem 0;
    font-size: 1.1rem;
  }
  /* .actions {
    display: flex;
    justify-content: center;
    gap: 10px;
  } */

  /* .actions button {
    padding: 10px 20px;
    border: none;
    border-radius: 6px;
    color: white;
    font-weight: bold;
    cursor: pointer;
  } */

  /* .actions button:last-child {
    background: white;
    color: black;
    border: 1px solid #999;
  } */

  /* .unavailable {
    text-align: center;
    padding: 50px;
  } */

  /* .sad-face {
    font-size: 60px;
    color: #bbb;
    margin-bottom: 20px;
  }  */
</style>
