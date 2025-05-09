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
})

// color button
const color = computed(() => {

  const category = currentProduct.value.category;
  if(category === "women's clothing"){
    return "#720060"
  }

  if(category === "men's clothing"){
    return "#002772"
  }

  return "#1e1e1e";
})

const nextProduct = () => {
  if(currentIndex.value < products.value.length - 1) {
    currentIndex.value++
  } else {
    currentIndex.value = 0
  }
}

const filledCircles = computed(() => {
  if (!currentProduct.value || !currentProduct.value.rating) {
    return [];
  }

  const rating = currentProduct.value.rating.rate;
  const full = Math.floor(rating);
  const half = rating % 1 >= 0.5 ? 1 : 0;
  const empty = 5 - full - half;

  return [
    ...Array(full).fill('full'),
    ...Array(half).fill('half'),
    ...Array(empty).fill('empty')
  ];
});



</script>

<template>
  <div :class="['wrapper', currentCategoryClass]">
    <div class="card" v-if="currentProduct"> 
      <div class="product-image">
        <img :src="currentProduct.image" alt="Product Image" />
      </div>
      <div class="content-section">
        <h2 :style="{ color: color }" class="title">{{ currentProduct.title }}</h2>
        <div class="category-rating">
          <p class="category">{{ currentProduct.category }}</p>
          <div class="rating">{{ currentProduct.rating.rate }} 
            <div class="stars">
              <!-- <div class="lingkaran"></div>
              <div class="lingkaran"></div>
              <div class="lingkaran"></div>
              <div class="lingkaran"></div>
              <div class="lingkaran"></div> -->
              <div
                v-for="(type, index) in filledCircles"
                :key="index"
                :style="type !== 'empty' ? { background: color } : {}"
                :class="['star', type]"
              ></div>
            </div>
          </div>
        </div>

        <p class="description">{{ currentProduct.description }}</p>
        <p class="price">${{ currentProduct.price }}</p>
        <div class="buttons"  >
          <button :style="{ backgroundColor: color }">Beli Sekarang!</button>
          <button :style="{ backgroundColor: 'white', color: 'black', border: `3px solid ${color}` }" class="next" @click="nextProduct">Produk Selanjutnya</button>
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

<style src="/src/style.css">
  
</style>
