<template>
  <section :class="sectionClass">
    <div class="product-bg">
      <div v-if="isLoading" class="spinner"></div>
      <div v-else-if="product" class="card-content">
        <div class="card-image">
          <img class="img-product" :src="product.image" :alt="product.title" />
        </div>
        <div class="product-details">
          <h2 class="product-title">{{ product.title }}</h2>
          <div class="category-rating">
            <span>{{ product.category }}</span>
            <span class="rating">
              <span class="rating-number">{{ product.rating.rate }}/5</span>
              <span
                v-for="n in 5"
                :key="n"
                :class="['circle', { filled: n <= product.rating.rate }]"
              ></span>
            </span>
          </div>
          <hr class="divider" />
          <p class="product-description">{{ product.description }}</p>
          <hr class="divider" />
          <p class="product-price">${{ product.price }}</p>
          <div class="button-group">
            <button class="button-primary">Buy Now</button>
            <button class="button-secondary" @click="nextProduct">
              Next Product
            </button>
          </div>
        </div>
      </div>
      <div v-else class="product-unavail">
        <img src="/sad-face.svg" alt="sad-face" />
        <p>Product unavailable to show</p>
        <button class="button-secondary" @click="nextProduct">
          Next Product
        </button>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";

const product = ref(null);
const currentIndex = ref(1);
const maxIndex = 20;
const isLoading = ref(false);
const categories = ["men's clothing", "women's clothing"];

const fetchProduct = async (index) => {
  try {
    isLoading.value = true;
    const response = await fetch(`https://fakestoreapi.com/products/${index}`);
    const data = await response.json();

    if (categories.includes(data.category)) {
      product.value = data;
    } else {
      nextProduct();
    }
  } catch (error) {
    console.error("Error fetching product:", error);
  } finally {
    isLoading.value = false;
  }
};

const nextProduct = () => {
  currentIndex.value += 1;
  if (currentIndex.value > maxIndex) {
    product.value = null;
  } else {
    fetchProduct(currentIndex.value);
  }
};

const sectionClass = computed(() => {
  if (product.value && product.value.category === "men's clothing") {
    return "men-section";
  } else if (product.value && product.value.category === "women's clothing") {
    return "women-section";
  } else {
    return "unavail-section";
  }
});

onMounted(() => {
  fetchProduct(currentIndex.value);
});
</script>

<style src="../styles/styles.css"></style>
