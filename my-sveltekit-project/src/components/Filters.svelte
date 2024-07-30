<script>
    export let searchTerm;
    export let setSearchTerm;
    export let filter;
    export let setFilter;
    export let sortOrder;
    export let setSortOrder;
    export let resetFilters;
    export let categories;
  </script>
  
  <div class="flex flex-col md:flex-row md:justify-between md:items-center mb-4">
    <input
      type="text"
      bind:value={searchTerm}
      on:input={() => setSearchTerm(searchTerm)}
      placeholder="Search categories..."
      class="bg-gray-200 text-gray-900 px-4 py-2 rounded mb-4 md:mb-0"
    />
    <div class="flex flex-col md:flex-row md:space-x-4 mt-4 md:mt-0">
      <select
        bind:value={filter}
        on:change={() => setFilter(filter)}
        class="bg-gray-200 text-gray-900 px-4 py-2 rounded"
      >
        <option value="">All Categories</option>
        {#each categories as category}
          <option value={category}>{category}</option>
        {/each}
      </select>
      <select
        bind:value={sortOrder}
        on:change={() => setSortOrder(sortOrder)}
        class="bg-gray-200 text-gray-900 px-4 py-2 rounded"
      >
        <option value="asc">Price: Low to High</option>
        <option value="desc">Price: High to Low</option>
      </select>
      <button on:click={resetFilters} class="bg-red-500 text-white px-4 py-2 rounded">Reset Filters</button>
    </div>
  </div>
  