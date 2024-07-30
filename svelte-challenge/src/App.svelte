<script>
  import { onMount } from 'svelte';
  import { writable, get } from 'svelte/store';
  import Navbar from './Navbar.svelte';
  import ProductList from './ProductList.svelte';
  import ProductModal from './ProductModal.svelte';
  import './lib/Tailwindcss.svelte'; // Import Tailwind CSS setup

  let products = writable([]);
  let filteredProducts = writable([]);
  let categories = writable([]);
  let searchQuery = writable("");
  let selectedCategory = writable("");
  let sortOrder = writable("");
  let favorites = writable(JSON.parse(localStorage.getItem("favorites")) || []);
  let showModal = writable(false);
  let modalProduct = writable({});
  let loading = writable(true);

  onMount(async () => {
    await fetchCategories();
    await fetchProducts();
  });

  async function fetchProducts() {
    loading.set(true);
    const response = await fetch("https://fakestoreapi.com/products");
    const data = await response.json();
    products.set(data);
    filteredProducts.set(data);
    loading.set(false);
  }

  async function fetchCategories() {
    const response = await fetch("https://fakestoreapi.com/products/categories");
    const data = await response.json();
    categories.set(data);
  }

  function openModal(product) {
    loading.set(true);
    fetch(`https://fakestoreapi.com/products/${product.id}`)
      .then((response) => response.json())
      .then((data) => {
        modalProduct.set(data);
        showModal.set(true);
        loading.set(false);
      });
  }

  function closeModal() {
    showModal.set(false);
  }

  function filterProducts() {
    loading.set(true);
    products.update((prods) => {
      filteredProducts.set(
        prods.filter((product) => {
          const matchesSearch = product.title
            .toLowerCase()
            .includes(get(searchQuery).toLowerCase());
          const matchesCategory =
            get(selectedCategory) === "" ||
            product.category === get(selectedCategory);
          return matchesSearch && matchesCategory;
        })
      );
    });
    sortProducts();
    loading.set(false);
  }

  function sortProducts() {
    filteredProducts.update((prods) => {
      if (get(sortOrder)) {
        prods.sort((a, b) => {
          if (get(sortOrder) === "asc") {
            return a.price - b.price;
          } else if (get(sortOrder) === "desc") {
            return b.price - a.price;
          }
          return 0;
        });
      }
      return prods;
    });
  }

  function toggleFavorite(productId) {
    favorites.update((favs) => {
      if (favs.includes(productId)) {
        return favs.filter((id) => id !== productId);
      } else {
        favs.push(productId);
      }
    });
    localStorage.setItem("favorites", JSON.stringify(get(favorites)));
  }

  function isFavorite(productId) {
    return get(favorites).includes(productId);
  }
</script>

<main class="bg-blue-200 min-h-screen">
  <Navbar />
  <div class="container mx-auto p-6">
    <ProductList
      {products}
      {filteredProducts}
      {categories}
      {searchQuery}
      {selectedCategory}
      {sortOrder}
      {favorites}
      {loading}
      on:filterProducts={filterProducts}
      on:sortProducts={sortProducts}
      on:toggleFavorite={toggleFavorite}
      on:isFavorite={isFavorite}
      on:openModal={openModal}
    />
  </div>
  {#if $showModal}
    <ProductModal {modalProduct} on:closeModal={closeModal} />
  {/if}
</main>
