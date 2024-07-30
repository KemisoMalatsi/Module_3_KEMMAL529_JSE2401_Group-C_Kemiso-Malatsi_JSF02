<script>
    export let modalProduct;
    export let closeModal;
  </script>
  
  <div class="modal fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 text-center" on:click.self={closeModal}>
    <div class="modal-content bg-white p-6 rounded shadow-lg w-full max-w-md">
      <button class="text-red-500" on:click={closeModal}>❎</button>
      <h3 class="text-xl font-bold mt-4">{$modalProduct.title}</h3>
      <img src={$modalProduct.image} alt="" class="w-full h-48 object-cover my-4" />
      <p class="text-gray-700">${$modalProduct.price}</p>
      <p class="text-gray-500">{$modalProduct.category}</p>
      <p class="text-gray-600">{$modalProduct.description}</p>
      <p class="text-gray-700 mt-2">Rating: {$modalProduct.rating.rate} ({$modalProduct.rating.count} reviews)</p>
      <button class="bg-blue-600 px-4 py-1 text-pink-50">Add To Cart +</button>
    </div>
  </div>
  