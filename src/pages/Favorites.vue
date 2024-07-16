<script setup>

import {onMounted, ref} from "vue";
import axios from "axios";
import CardList from "@/components/cardList.vue";

const favorites= ref([])

onMounted( async () =>{
  try {
    const {data} = await axios.get('https://b7ed7504a1815ece.mokky.dev/favorites?_relations=items')

  favorites.value = data.map(obj => obj.item)
  } catch (err){
    console.log(err)
  }
});
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Мои закладки</h2>
  <card-list :items="favorites" is-favorites/>
</template>

<style scoped>

</style>