<script setup>
import {computed, onMounted, ref} from "vue"
import card from './components/Card.vue'
import order from './components/Send-order.vue'

const products = ref([])
//const filteredProducts=ref([])
const searchTitle=ref("")
const priseFrom=ref()
const priseTo=ref()
const isOrder=ref(false)

const filteredProducts=computed(()=>{
  return products.value.filter(
    x=>{
      const title=x.title.toLowerCase().includes(searchTitle.value.toLowerCase());
      const minPrice = priseFrom.value ? Number(priseFrom.value) : 0;
      const maxPrice = priseTo.value ? Number(priseTo.value) : Infinity;
      const prise = Number(x.price)>=minPrice && Number(x.price)<=maxPrice;
      return title && prise;
    }
  )
})

// onMounted(() => {
//     fetch('/src/assets/products.json')
//     .then(res=>res.json())
//     .then(data=>{
//       products.value=data
//       //filteredProducts.value=products.value
//     });
//   })

onMounted(async() => {
  try{
    const response=await fetch('https://fakestoreapi.com/products');
    products.value=await response.json();
  }
  catch(error){
    console.log("Ошибка загрузки:", error);
  };
  })
  
</script>

<template>
  <div>Поиск</div>
  <label>по наименованию</label>
  <input type="text" v-model="searchTitle"></input><br/><br/>
  <label>по цене</label><br/>
  <span>от</span><input type="number" v-model="priseFrom"></input>
  <span>до</span><input type="number" v-model="priseTo"></input>
  <div>
    <button v-on:click="isOrder=!isOrder">Сделать заказ</button>
  </div>
  <order v-if="isOrder" :products="products">

  </order>

  <card 
  v-for="product in filteredProducts" 
  :product="product"
  ></card>

</template>

<style scoped>
input{
  margin-left: 10px;
  margin-right: 5px;
}
button{
  margin-top: 10px;
}
</style>
