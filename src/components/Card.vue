<script setup>
import {ref} from "vue"

const props = defineProps(["product"])

</script>

<template>
  
    <h2>{{ product.id }}. {{ product.title }}</h2>
    <img :src="product.image" alt="Image"></img>
    <div>Description: {{ product.description }}</div>
    <h4>Category: {{ product.category }}</h4>
    <h3>Price: {{ product.price }}</h3>
    <div> 
      Rate: {{ product.rating.rate }}
      <span>Count: {{ product.rating.count }}</span>
    </div>


  
</template>

<style scoped>

</style>
