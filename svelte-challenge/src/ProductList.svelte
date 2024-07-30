<script>
    import { get } from 'svelte/store';
    import ProductItem from './ProductItem.svelte';
  
    export let products;
    export let filteredProducts;
    export let categories;
    export let searchQuery;
    export let selectedCategory;
    export let sortOrder;
    export let favorites;
    export let loading;
    export let filterProducts;
    export let sortProducts;
    export let toggleFavorite;
    export let isFavorite;
    export let openModal;
  </script>
  
  <div class="container mx-auto p-6">
    <div class="flex justify-between mb-4">
      <select bind:value={selectedCategory} on:change={filterProducts} class="border p-2 rounded">
        <option value="">All Categories</option>
        {#each $categories as category}
          <option value={category}>{category}</option>
        {/each}
      </select>
      <div class="flex items-center">
        <input type="text" bind:value={searchQuery} placeholder="Search products..." class="border p-2 rounded-l" />
        <button on:click={filterProducts} class="bg-blue-500 text-black hover:bg-white border border-l-0 p-2 rounded-r">Search</button>
      </div>
      <select bind:value={sortOrder} on:change={sortProducts} class="border p-2 rounded">
        <option value="">Sort by Price</option>
        <option value="asc">Lowest to Highest</option>
        <option value="desc">Highest to Lowest</option>
      </select>
    </div>
  
    {#if $loading}
      <div class="loading text-center text-2xl">Loading...</div>
    {/if}
  
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
      {#each $filteredProducts as product (product.id)}
        <ProductItem
          {product}
          {favorites}
          on:toggleFavorite={toggleFavorite}
          on:isFavorite={isFavorite}
          on:openModal={openModal}
        />
      {/each}
    </div>
  </div>
  