<script setup>
import DropDown from '../components/dropDown.vue';
import { ref, onMounted, computed } from 'vue';

const selected = ref(null);
const categories = ref([]);
const CATEGORIES_TOKEN = import.meta.env.VITE_CATEGORIES_TOKEN;

const filteredCategories = computed(() => {
  if (!selected.value) return categories.value;

  return categories.value.filter((parent) =>
    parent.children?.some((child) => child.id === selected.value)
  );
});

onMounted(async () => {
  try {
    const res = await fetch(
      'https://api-staging.orderez.co/distributor/categories',
      {
        method: 'GET',
        headers: {
          Authorization: `Bearer ${CATEGORIES_TOKEN}`,
          'Content-Type': 'application/json',
        },
      }
    );

    if (!res.ok) throw new Error(`HTTP error! Status: ${res.status}`);

    const data = await res.json();
    categories.value = data.data;
    console.log('Fetched categories:', data);
  } catch (err) {
    console.error('Fetch failed:', err);
  }
});
</script>

<template>
  <div class="page">
    <section class="dropdown-section">
      <h1>Select a Category</h1>
      <p class="selected-text">
        Selected (represents the ID of the category): {{ selected }}
      </p>
      <DropDown v-model="selected" :options="categories" />
    </section>

    <section class="products-section">
      <h2>Explore Our Products</h2>
      <div class="product-grid">
        <div
          v-for="item in filteredCategories"
          :key="item.id"
          class="product-card"
        >
          <img :src="item.image" alt="Product image" />
          <p class="product-name">{{ item.name }}</p>
        </div>
      </div>
    </section>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Manrope:wght@400;600&display=swap');

.page {
  font-family: 'Manrope', sans-serif;
  padding: 40px 20px;
  max-width: 1200px;
  margin: 0 auto;
}

h1,
h2 {
  font-weight: 600;
  margin-bottom: 16px;
  color: #333;
}

.selected-text {
  margin-top: 10px;
  font-size: 14px;
  color: #666;
}

.dropdown-section {
  margin-bottom: 40px;
}

.products-section {
  margin-top: 40px;
}

.product-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.product-card {
  flex: 1 1 calc(20% - 20px);
  background-color: #fff;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.05);
  text-align: center;
  padding: 12px;
  transition: transform 0.2s;
}

.product-card:hover {
  transform: translateY(-5px);
}

.product-card img {
  max-width: 100%;
  height: auto;
  border-radius: 8px;
}

.product-name {
  margin-top: 10px;
  font-size: 14px;
  color: #444;
}

/* Responsive layout - calculates the items as per window width */
@media (max-width: 1200px) {
  .product-card {
    flex: 1 1 calc(25% - 20px);
  }
}

@media (max-width: 992px) {
  .product-card {
    flex: 1 1 calc(33.333% - 20px);
  }
}

@media (max-width: 768px) {
  .product-card {
    flex: 1 1 calc(50% - 20px);
  }
}

@media (max-width: 480px) {
  .product-card {
    flex: 1 1 100%;
  }
}
</style>
