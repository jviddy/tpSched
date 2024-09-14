<template>
    <div class="card text-center">
        <img :src="product.image" alt="product image" class=" thumb w-1/2 mx-auto" />
        <div class="card-body">
            <h2 class="card-title font-bold text-grey-500 m-4 truncate">{{ product.title }}</h2>
            <nuxt-link :to="`/blog/${product.id}`" class="btn btn-primary my-4">View Product</nuxt-link>
            <p class="card-text">{{ product.description }}</p>
            <p class="card-text">{{ product.price }}</p>
            <p class="card-text">{{ product.category }}</p>
            <p class="card-text">{{ product.id }}</p>
        </div>
    </div>
</template>

<script setup>
    const { product } = defineProps(['product'])
</script>

<style  scoped>  
    .thumb {
        height: 100px;
        width: 100px;
        border-radius: 10px;
    }
</style>

