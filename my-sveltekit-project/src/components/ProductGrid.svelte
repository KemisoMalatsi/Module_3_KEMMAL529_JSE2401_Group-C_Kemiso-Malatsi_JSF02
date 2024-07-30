<script>
    import ProductCard from './ProductCard.svelte';
    export let products;
    export let navigateToProductDetail;
    export let toggleWishlist;
    export let toggleCart;
    export let wishlist;
    export let cart;
  </script>
  
  <div class="grid">
    {#each products as product}
      <ProductCard
        {product}
        {navigateToProductDetail}
        {toggleWishlist}
        {toggleCart}
        {wishlist}
        {cart}
      />
    {/each}
  </div>
  
  <style>
    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
      gap: 16px;
    }
  </style>
  