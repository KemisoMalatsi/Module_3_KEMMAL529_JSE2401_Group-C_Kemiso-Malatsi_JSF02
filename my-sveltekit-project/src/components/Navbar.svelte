<script>
    export let wishlistLength;
    export let cartLength;
    export let showWishlistItems;
    export let showCartItems;
  </script>
  
  <header class="navbar">
    <nav class="max-w-screen-xl flex items-center justify-between mx-auto p-4">
      <a href="/" class="text-2xl font-semibold">SwiftCart</a>
      <div class="flex space-x-4">
        <a href="#" on:click={showWishlistItems}>Wishlist ({wishlistLength})</a>
        <a href="#" on:click={showCartItems}>Cart ({cartLength})</a>
        <a href="/login">Login</a>
        <a href="/register">Register</a>
      </div>
    </nav>
  </header>
  
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
  </style>
  