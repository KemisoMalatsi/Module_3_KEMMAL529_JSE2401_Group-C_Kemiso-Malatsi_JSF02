<script>
    export let product;
    export let navigateToProductDetail;
    export let toggleWishlist;
    export let toggleCart;
    export let wishlist;
    export let cart;
  </script>
  
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
      {#if wishlist.includes(product.id)}💖{/if}
      {#if !wishlist.includes(product.id)}🤍{/if}
    </button>
    <button class="cart-button" on:click={() => toggleCart(product.id)}>
      {#if cart.includes(product.id)}🛒{/if}
      {#if !cart.includes(product.id)}➕{/if}
    </button>
  </div>
  
  <style>
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
  