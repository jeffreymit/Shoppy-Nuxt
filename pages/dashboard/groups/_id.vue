<template>
  <div>
    <h1 class="font-bold text-3xl mb-10">
      Modify group {{ $route.params.id }}
    </h1>
    <div class="p-4 bg-white rounded-lg shadow-md mb-4">
      <div class="flex justify-between mb-5">
        <p>General Information</p>
      </div>
      <div class="mb-4 w-full">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="username">
          Title
        </label>
        <textarea 
          v-model="title"
          rows="5" 
          class="resize border rounded focus:outline-none focus:shadow-outline w-full"
          ></textarea>
      </div>
      <div class="mb-4 w-full">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="username">
          Products
        </label>
        <v-select 
          v-model="productsSelected"
          :options="products"
          :closeOnSelect="false"
          multiple
          label="label"
          class="w-full"
          placeholder="Type to search your products"
        />
      </div>
      <label class="flex items-center text-gray-500 font-bold mr- mb-2">
        <input class="mr-2 leading-tight" type="checkbox">
        <span class="text-sm">
          Unlisted
        </span>
      </label>
      <p>Only accessible using a link. Not visible on your profile.</p>
    </div>
    <div class="flex justify-end mb-2">
      <styled-button type="submit">
        Create Group
      </styled-button>
    </div>
  </div>
</template>

<script>
  export default {
    layout: 'dashboard',
    name: 'dashboardGroupsId',
    data() {
      return {
        unlisted: false,
        title: 'Product title',
        productsSelected: [
          {
            label: 'Product 1'
          },
          {
            label: 'Product 2'
          },
        ],
        products: [
          {
            label: 'Product 1'
          },
          {
            label: 'Product 2'
          },
          {
            label: 'Product 3'
          },
          {
            label: 'Product 4'
          },
        ]
      }
    }
  }
</script>

<style lang="scss">
</style>