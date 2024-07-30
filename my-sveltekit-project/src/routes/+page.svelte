<script>
  import { goto } from '$app/navigation';
  import { onMount } from 'svelte';
  import { navigating } from '$app/stores';
  import { writable } from 'svelte/store';

  import Navbar from '../components/Navbar.svelte';
  import ProductGrid from '../components/ProductGrid.svelte';
  import Filters from '../components/Filters.svelte';

  let products = [];
  let categories = [];
  let filteredCategories = [];
  let wishlist = writable([]);
  let cart = writable([]);
  let searchTerm = "";
  let sortOrder = "asc";
  let filter = "";
  let loading = false;
  let error = "";

  async function fetchProducts() {
    loading = true;
    error = "";
    try {
      const res = await fetch("https://fakestoreapi.com/products");
      if (!res.ok) throw new Error(`Failed to fetch products: ${res.statusText}`);
      let data = await res.json();
      if (filter) data = data.filter(product => product.category === filter);
      data = data.sort((a, b) => sortOrder === "asc" ? a.price - b.price : b.price - a.price);
      products = data;
    } catch (err) {
      console.error("Failed to fetch products:", err);
      error = "Failed to load products. Please try again later.";
    }
    loading = false;
  }

  async function fetchCategories() {
    try {
      const res = await fetch("https://fakestoreapi.com/products/categories");
      if (!res.ok) throw new Error(`Failed to fetch categories: ${res.statusText}`);
      categories = await res.json();
      filteredCategories = categories;
    } catch (err) {
      console.error("Failed to fetch categories:", err);
      error = "Failed to load categories. Please try again later.";
    }
  }

  function filterCategories() {
    const term = searchTerm.toLowerCase();
    filteredCategories = categories.filter(category => category.toLowerCase().includes(term));
  }

  function resetFilters() {
    filter = "";
    sortOrder = "asc";
    searchTerm = "";
    filteredCategories = categories;
    fetchProducts();
  }

  function navigateToProductDetail(productId) {
    goto(`/product/${productId}`);
  }

  function toggleWishlist(productId) {
    wishlist.update(wishlist => wishlist.includes(productId) ? wishlist.filter(id => id !== productId) : [...wishlist, productId]);
  }

  function toggleCart(productId) {
    cart.update(cart => cart.includes(productId) ? cart.filter(id => id !== productId) : [...cart, productId]);
  }

  function clearWishlist() {
    wishlist.set([]);
  }

  function clearCart() {
    cart.set([]);
  }

  async function showWishlistItems() {
    loading = true;
    try {
      const res = await fetch("https://fakestoreapi.com/products");
      if (!res.ok) throw new Error(`Failed to fetch wishlist items: ${res.statusText}`);
      const data = await res.json();
      wishlist.subscribe(currentWishlist => {
        products = data.filter(product => currentWishlist.includes(product.id));
      });
    } catch (err) {
      console.error("Failed to fetch wishlist items:", err);
      error = "Failed to load wishlist items. Please try again later.";
    }
    loading = false;
  }

  async function showCartItems() {
    loading = true;
    try {
      const res = await fetch("https://fakestoreapi.com/products");
      if (!res.ok) throw new Error(`Failed to fetch cart items: ${res.statusText}`);
      const data = await res.json();
      cart.subscribe(currentCart => {
        products = data.filter(product => currentCart.includes(product.id));
      });
    } catch (err) {
      console.error("Failed to fetch cart items:", err);
      error = "Failed to load cart items. Please try again later.";
    }
    loading = false;
  }

  onMount(() => {
    fetchProducts();
    fetchCategories();
  });
</script>

<main>
  {#if $navigating}
    <div class="loading-overlay">
      <div class="loading-spinner"></div>
    </div>
  {/if}

  {#if error}
    <p class="text-red-500">{error}</p>
  {/if}

  {#if loading}
    <p>Loading...</p>
  {:else}
    <div class="container mx-auto p-4">
      <Navbar
        wishlistLength={$wishlist.length}
        cartLength={$cart.length}
        showWishlistItems={showWishlistItems}
        showCartItems={showCartItems}
      />

      <Filters
        {searchTerm}
        setSearchTerm={filterCategories}
        {filter}
        setFilter={fetchProducts}
        {sortOrder}
        setSortOrder={fetchProducts}
        resetFilters={resetFilters}
        {categories}
      />

      <ProductGrid
        {products}
        {navigateToProductDetail}
        {toggleWishlist}
        {toggleCart}
        {wishlist}
        {cart}
      />

      <button on:click={clearWishlist} class="mt-4 bg-yellow-500 text-white px-4 py-2 rounded">Clear Wishlist</button>
      <button on:click={clearCart} class="mt-4 bg-green-500 text-white px-4 py-2 rounded">Clear Cart</button>
    </div>
  {/if}
</main>

<style>
  .loading-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(255, 255, 255, 0.8);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000;
  }

  .loading-spinner {
    border: 4px solid rgba(255, 255, 255, 0.3);
    border-top: 4px solid #3498db;
    border-radius: 50%;
    width: 40px;
    height: 40px;
    animation: spin 1s linear infinite;
  }

  @keyframes spin {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
</style>
