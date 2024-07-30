<script>
    import { goto } from '$app/navigation';
    import { onMount } from 'svelte';
    import { navigating } from '$app/stores';
    import { writable } from 'svelte/store';
  
    // State variables
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
  
    // Fetch products
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
  
    // Fetch categories
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
  
    // Filter categories
    function filterCategories() {
      const term = searchTerm.toLowerCase();
      filteredCategories = categories.filter(category => category.toLowerCase().includes(term));
    }
  
    // Reset filters
    function resetFilters() {
      filter = "";
      sortOrder = "asc";
      searchTerm = "";
      filteredCategories = categories;
      fetchProducts();
    }
  
    // Navigate to product detail
    function navigateToProductDetail(productId) {
      goto(`/product/${productId}`);
    }
  
    // Toggle wishlist
    function toggleWishlist(productId) {
      wishlist.update(wishlist => wishlist.includes(productId) ? wishlist.filter(id => id !== productId) : [...wishlist, productId]);
    }
  
    // Toggle cart
    function toggleCart(productId) {
      cart.update(cart => cart.includes(productId) ? cart.filter(id => id !== productId) : [...cart, productId]);
    }
  
    // Clear wishlist
    function clearWishlist() {
      wishlist.set([]);
    }
  
    // Clear cart
    function clearCart() {
      cart.set([]);
    }
  
    // Show wishlist items
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
  
    // Show cart items
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
        <header class="navbar">
          <nav class="max-w-screen-xl flex items-center justify-between mx-auto p-4">
            <a href="/" class="text-2xl font-semibold">SwiftCart</a>
            <div class="flex space-x-4">
              <a href="#" on:click={showWishlistItems}>Wishlist ({$wishlist.length})</a>
              <a href="#" on:click={showCartItems}>Cart ({$cart.length})</a>
              <a href="/login">Login</a>
              <a href="/register">Register</a>
            </div>
          </nav>
        </header>
  
        <div class="flex flex-col md:flex-row md:justify-between md:items-center mb-4">
          <input type="text" bind:value={searchTerm} on:input={filterCategories} placeholder="Search categories..." class="bg-gray-200 text-gray-900 px-4 py-2 rounded mb-4 md:mb-0" />
          <div class="flex flex-col md:flex-row md:space-x-4 mt-4 md:mt-0">
            <select bind:value={filter} on:change={fetchProducts} class="bg-gray-200 text-gray-900 px-4 py-2 rounded">
              <option value="">All Categories</option>
              {#each filteredCategories as category}
                <option value={category}>{category}</option>
              {/each}
            </select>
            <select bind:value={sortOrder} on:change={fetchProducts} class="bg-gray-200 text-gray-900 px-4 py-2 rounded">
              <option value="asc">Price: Low to High</option>
              <option value="desc">Price: High to Low</option>
            </select>
            <button on:click={resetFilters} class="bg-red-500 text-white px-4 py-2 rounded">Reset Filters</button>
          </div>
        </div>
  
        <div class="grid">
          {#each products as product}
            <div class="card">
              <img src={product.image} alt={product.title} on:click={() => navigateToProductDetail(product.id)} />
              <h2 class="text-lg font-semibold">{product.title}</h2>
              <p class="text-gray-900">${product.price}</p>
              <p class="text-gray-600">{product.category}</p>
              <div class="rating">
                {#each Array(Math.round(product.rating.rate)) as _}⭐{/each}
                <span>({product.rating.count})</span>
              </div>
              <button class="wishlist-button" on:click={() => toggleWishlist(product.id)}>
                {#if $wishlist.includes(product.id)}💖{/if}
                {#if !$wishlist.includes(product.id)}🤍{/if}
              </button>
              <button class="cart-button" on:click={() => toggleCart(product.id)}>
                {#if $cart.includes(product.id)}🛒{/if}
                {#if !$cart.includes(product.id)}➕{/if}
              </button>
            </div>
          {/each}
        </div>
        <button on:click={clearWishlist} class="mt-4 bg-yellow-500 text-white px-4 py-2 rounded">Clear Wishlist</button>
        <button on:click={clearCart} class="mt-4 bg-green-500 text-white px-4 py-2 rounded">Clear Cart</button>
      </div>
    {/if}
  </main>
  
  <style>
    .navbar {
      position: sticky;
      top: 0;
      z-index: 50;
      background-color: #333;
    }
  
    .navbar a {
      color: white;
      padding: 10px;
      text-decoration: none;
      text-align: center;
    }
  
    .navbar a:hover {
      background-color: #ddd;
      color: black;
    }
  
    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
      gap: 16px;
    }
  
    .card {
      border: 1px solid #ddd;
      border-radius: 8px;
      overflow: hidden;
      padding: 16px;
      position: relative;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      transition: transform 0.2s, box-shadow 0.2s;
    }
  
    .card:hover {
      transform: scale(1.03);
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }
  
    .card img {
      max-width: 100%;
      height: auto;
      cursor: pointer;
      transition: transform 0.2s;
    }
  
    .card img:hover {
      transform: scale(1.05);
    }
  
    .wishlist-button,
    .cart-button {
      position: absolute;
      bottom: 8px;
      padding: 8px;
      border-radius: 50%;
      cursor: pointer;
    }
  
    .wishlist-button {
      left: 8px;
      background-color: yellow;
    }
  
    .cart-button {
      right: 8px;
      background-color: green;
      color: white;
    }
  
    .card h2 {
      margin-top: 10px;
      font-size: 1.25rem;
    }
  
    .card p {
      margin: 5px 0;
    }
  
    .rating {
      color: #f1c40f;
      margin: 5px 0;
    }
  </style>