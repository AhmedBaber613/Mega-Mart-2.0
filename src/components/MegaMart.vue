<template>
  <div>
    <!-- Header Section -->
    <header class="header">
      <p class="m">M</p>Mega Mart
      <div class="container1">
        <div class="drop-down-banner">
          <select id="dropdown" v-model="selectedCategory" @change="filterByCategory">
            <option value="all">All Categories</option>
            <option v-for="category in categories" :key="category" :value="category">
              {{ category }}
            </option>
          </select>
          <input
            type="text"
            placeholder="Search..."
            style="outline: none;"
            class="input"
            v-model="searchQuery"
            @input="debouncedSearch"
          />
        </div>
        <button class="button1">
          <img src="" alt="Cart Icon" height="20px" width="20px" />
          <span>Cart</span>
        </button>
      </div>
    </header>

    <!-- Results Section -->
    <div class="parent">
      <h1 class="header2">Results</h1>
      <div class="parent-container">
        <div
          v-for="(product, index) in filteredProducts"
          :key="product.id"
          :class="index < 3 ? 'pro-max1' : 'pro-max'"
          :data-category="product.category"
        >
          <img :src="product.image" class="img" />
          <h2>{{ product.title }}</h2>
          <p class="desc">{{ product.description.substring(0, 100) + '...' }}</p>
          <p class="price">${{ product.price }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from "vue";

export default {
  name: "Megamart",
  setup() {
    // API URL for fetching products
    const apiUrl = "https://fakestoreapi.com/products";

    // Reactive state for storing products and categories
    const products = ref([]); // All fetched products
    const categories = ref(new Set()); // Unique product categories

    // State for user selections
    const selectedCategory = ref("all"); // Default: show all categories
    const searchQuery = ref(""); // For search input

    /**
     * Fetch products from the API and extract unique categories.
     */
    const fetchProducts = async () => {
      try {
        const response = await fetch(apiUrl);
        if (!response.ok) {
          throw new Error(`Error fetching data: ${response.status}`);
        }
        const data = await response.json();
        products.value = data; // Save fetched products
        // Extract unique categories
        data.forEach((product) => categories.value.add(product.category));
      } catch (error) {
        console.error("An error occurred", error);
      }
    };

    /**
     * Compute filtered products based on selected category and search query.
     */
    const filteredProducts = computed(() => {
      return products.value.filter((product) => {
        const matchesCategory =
          selectedCategory.value === "all" ||
          product.category === selectedCategory.value;
        const matchesSearch =
          product.title.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
          product.category.toLowerCase().includes(searchQuery.value.toLowerCase());
        return matchesCategory && matchesSearch;
      });
    });

    /**
     * Debounce function to delay search query updates.
     * Helps reduce the number of unnecessary filter operations.
     */
    const debounce = (func, wait) => {
      let timeout;
      return (...args) => {
        clearTimeout(timeout);
        timeout = setTimeout(() => func(...args), wait);
      };
    };

    const debouncedSearch = debounce(() => {}, 300); // 300ms delay

    /**
     * Filter by category. Currently unused but placeholder for functionality.
     */
    const filterByCategory = () => {};

    /**
     * Fetch products when the component is mounted.
     */
    onMounted(() => {
      fetchProducts();
    });

    // Expose reactive variables and computed values to the template
    return {
      products,
      categories,
      selectedCategory,
      searchQuery,
      filteredProducts,
      debouncedSearch,
    };
  },
};
</script>

<style>
.parent {
  flex-direction: row;
}

.header button {
  padding-left: 40px;
  padding-right: 40px;
  position: absolute;
  left: 90%;
  padding-top: 15px;
  padding-bottom: 15px;
  color: white;
  background-color: #ffff;
  border: none;
  margin-top: 0.5%;
}

.button1:hover {
  cursor: pointer;
}

.products-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: start; /* Align items at the start of the container */
  gap: 20px; /* Adjust based on your preference */
}

.header {
  margin-top: -59px;
  margin-left: -685px;
  margin-right: -8px;
  font-family: sans-serif;
  color: #008ed1;
  background-color: #ffff;
  padding-top: 26px;
  padding-bottom: 39px;
  padding-left: 78px;
  list-style-type: none;
  font-size: 32px;
}

.pro-max {
  border-radius: 10px;
  display: inline-block;
  background-color: #ffffff;
  padding: 15px;
  text-align: center;
  font-family: sans-serif;
  font-size: 13px;
  margin-top: 50px;
  margin-bottom: 20px;
  margin-right: 0px;
  margin-left: 50px;
  position: relative;
  overflow: hidden;
  width: 305px;
}

.pro-max1 {
  border-radius: 10px;
  display: inline-block;
  background-color: #ffffff;
  padding: 15px;
  text-align: center;
  font-family: sans-serif;
  font-size: 13px;
  margin-top: 150px;
  margin-bottom: 20px;
  margin-right: 0px;
  margin-left: 50px;
  position: relative;
  overflow: hidden;
  width: 305px;
}

.pro-max .img {
  width: 225px;
  height: 312px;
  object-fit: cover;
  transition: transform 0.3s ease-in-out;
  z-index: 1;
}

.pro-max1 .img {
  width: 225px;
  height: 312px;
  object-fit: cover;
  transition: transform 0.3s ease-in-out;
}

.img {
  width: 225px;
  height: 312px;
  object-fit: cover;
  transition: transform 0.3s ease-in-out;
}

.pro-max:hover {
  cursor: pointer;
  object-fit: cover;
  transition: transform 0.3s ease-in-out;
}

.pro-max1:hover {
  cursor: pointer;
}

body {
  background-color: #d1d5db;
}

.header2 {
  font-family: Arial;
  color: #008ed1;
  padding-top: 20px;
  padding-bottom: 20px;
  font-weight: 100;
  position: relative;
  top: 9%;
  left: 5%;
}

input {
  width: 500px;
  height: 40px;
  padding: 8px;
}

@media (max-width: 420px) {
  .header {
    font-size: 18px;
  }

  .pro-max1 {
    margin-top: 60px;
  }

  .m {
    font-size: 22px;
  }
}

@media (max-width: 1110px) {
  .header button {
    margin-left: 8px;
    padding-right: 20px;
    padding-top: 8px;
    padding-bottom: 8px;
  }
}

@media (max-width: 1426px) {
  .container1 {
    width: 78px;
    white-space: nowrap;
  }
}

@media (max-width: 1100px) {
  .container1 input {
    width: 800px;
  }
}

@media (max-width: 1098px) {
  .container1 input {
    width: 350px;
  }
}

.drop-down-banner {
  display: inline-block;
  background-color: #f3f9fb;
  margin-left: 60px;
  border-radius: 6px;
  position: relative;
  left: 50%;
}

.button1 {
  position: relative;
  top: 25px;
  color: #000000;
  right: 50px;
  white-space: nowrap;
  background-color: #ffffff;
}

#dropdown {
  color: #7e7e7e;
  border: none;
  border-radius: 15px;
  margin-right: 12px;
  margin-left: 8px;
}

#dropdown:hover {
  cursor: pointer;
  border-radius: 15px;
}

.input {
  background-color: #f3f9fb;
  border: none;
  border-radius: 15px;
}

.input::focus {
  outline: none;
  border: #afacac dashed 3px;
  border-radius: 15px;
}

span {
  color: black;
  margin-right: 8px;
  margin-left: 8px;
  font-weight: bold;
  color: #555353;
  font-family: sans-serif;
}

.parent {
  display: flex;
}

/* Style for the overlay */
.overlay {
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5); /* Semi-transparent black */
  opacity: 0; /* Initially hidden */
  transition: 0.3s ease; /* Smooth transition */
  position: absolute;
  z-index: 1;
}

/* Style for the button (adjust as needed) */
.button {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #fff;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  font-family: sans-serif;
  cursor: pointer;
  z-index: 2;
}

.pro-max:hover .overlay {
  opacity: 1;
  transition: opacity 0.5s ease;
  border-radius: 10px;
}

.button:hover {
  opacity: 1;
  background-color: #ffae00;
  color: #ffffff;
  border: none;
  transition: 0.3s ease;
}

.pro-max1:hover .overlay {
  opacity: 1;
  transition: opacity 0.5s ease;
  border-radius: 10px;
}

select {
  outline: none;
  background-color: #f3f9fb;
  border-radius: 15px;
}

.pro-max:hover img {
  transform: scale(1.2);
}

.pro-max1:hover img {
  transform: scale(1.2);
}

.m {
  display: inline-block;
  margin: 0 10px;
  font-size: 28px;
  color: #008ed1;
  background-color: #f3f9fb;
  padding: 6px 9px;
  border: thin solid #e5e7eb;
  border-radius: 10px;
}

.container1 {
  border-radius: 15px;
  display: inline-block;
}

@media (max-width: 420px) {
  .m {
    font-size: 22px;
  }

  .drop-down-banner {
    width: 100px;
  }

  .input {
    width: 200px;
  }
}

.button1 img {
  position: absolute;
  left: 15%;
  bottom: 28%;
}

.price {
  font-size: 37px;
}

.desc {
  display: inline-block;
  color: black;
}
</style>
