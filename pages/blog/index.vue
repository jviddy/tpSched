<template>
    <h1>Products page</h1>
    <div class="grid grid-cols-4 gap-4">
      <div v-for="p in products" :key="p.id">
        <ProductCard :product="p" />
      </div>
      
      
    </div>
  </template>
  
  <script setup>
  // You can add any necessary imports here
   definePageMeta({
    layout: 'blog',
   })
   
   //fetch data from fakestoreapi
  const {data: products} = await useFetch('https://fakestoreapi.com/products')
  // Add any component logic here
  </script >
  
  
  <style scoped>
  /* Add any component-specific styles here */
  </style>
  