<template>
    <div>
        <p>blog post for {{ id }}</p>
        <p> {{ product.title }}</p>
        <p> {{ product.description }}</p>
        <p> {{ product.price }}</p>
        <p> {{ product.category }}</p>
        <p> {{ product.image }}</p>
        <p> {{ product.id }}</p>
    </div>
</template>

<script setup> 

//set page layout to blog
definePageMeta({
    layout: 'blog',
   })

const {id} = useRoute().params;
const uri = 'https://fakestoreapi.com/products/' + id;
//fetch data from fakestoreapi
const {data: product} = await useFetch(uri, {key: id })
console.log(product.value)
</script>

<style lang="scss" scoped>

</style>