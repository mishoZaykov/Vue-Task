<template>
  <div id="app">
    <div class="filters">
      <select v-model="selectedCategory">
        <option value="">All Categories</option>
        <option v-for="category in categories" :value="category.slug" :key="category.slug">
          {{ category.name }}
        </option>
      </select>
      <select v-model="orderBy">
        <option value="name">Order by Name</option>
        <option value="price">Order by Price</option>
      </select>
    </div>
    <div class="product-list">
      <div v-for="product in filteredProducts" :key="product.id" class="product-card">
        <img :src="product.images[0].src" :alt="product.name" style="width: 100%;" />
        <h3>{{ product.name }}</h3>
        <p>{{ product.description }}</p>
        <p>Category: {{ product.categories[0].name }}</p>
        <add-to-cart-button :product-id="product.id" />
      </div>
    </div>
    <button v-if="hasMorePages" class="load-more" @click="loadMore">Load More</button>
      <!-- <button v-infinite-scroll="loadMore" infinite-scroll-disabled="!hasMorePages" infinite-scroll-distance="scrollDistance">
        Load More
      </button> -->
  </div>
</template>

<script>
import axios from 'axios';
import AddToCartButton from './components/AddToCartButton.vue';

export default {
  data() {
    return {
      products: [],
      categories: [],
      selectedCategory: '',
      orderBy: 'name',
      currentPage: 1,
      hasMorePages: true,
    };
  },
  components: {
    'add-to-cart-button': AddToCartButton,
  },
  computed: {
    filteredProducts() {
      let filtered = [...this.products];

      if (this.selectedCategory) {
        filtered = filtered.filter((product) =>
          product.categories.some((category) => category.slug === this.selectedCategory)
        );
      }

      if (this.orderBy === 'name') {
        filtered.sort((a, b) => a.name.localeCompare(b.name));
      } else if (this.orderBy === 'price') {
        filtered.sort((a, b) => a.price - b.price);
      }

      return filtered;
    },
  },
  mounted() {
    this.loadProducts();
    this.loadCategories();
  },
  methods: {
    loadProducts() {
      axios
        .get(`https://greet.bg/wp-json/wc/store/products?page=${this.currentPage}`)
        .then((response) => {
          this.products = [...this.products, ...response.data];
          this.currentPage++;
          console.log(response.headers);
          this.hasMorePages = response.headers['x-wp-totalpages'].length > this.currentPage;
        })
        .catch((error) => {
          console.error('Error loading products', error);
        });
    },
    loadCategories() { 
      axios
        .get('https://greet.bg/wp-json/wc/store/products/categories')
        .then((response) => {
          this.categories = response.data;
        })
        .catch((error) => {
          console.error('Error loading categories', error);
        });
    },
    loadMore() {
      if(this.hasMorePages){
        this.loadProducts();
      }
    },
  },
};
</script>

<style>
  body{
    background-color: #ffe5d9;
  }
  .product-card {
  background-color: #fec89a;
  border: 1px solid #fec5bb;
  border-radius: 10px;
  margin: 10px;
  padding: 10px;
  width: 300px;
  display: inline-block;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  align-items: center;
}

.product-card img {
  max-width: 100%;
  height: 50%;
}

.product-card h3 {
  font-size: 1.2rem;
  margin: 10px 0;
}

.product-card p {
  font-size: 1rem;
  color: #495057;
}

.filters {
  margin-bottom: 20px;
}

.filters select {
  padding: 5px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-right: 10px;
}

.load-more{
  width: 100%;
  height: 50px;
  border: 2px solid #ffd7ba;
  border-radius: 8px;
  padding: 5px;
  text-decoration: none;
  background: #fec5bb;
  color: inherit;
  cursor: pointer;
}
</style>
