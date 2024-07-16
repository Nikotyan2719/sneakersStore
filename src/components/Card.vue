<script setup>
defineProps({
  id: Number,
  title: String,
  imageUrl: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickAdd: Function,
  onClickFavorite: Function
})


</script>

<template>
<div class="relative bg-white flex flex-col w-full border border-slate-100 rounded-3xl p-8 cursor-pointer transition hover:shadow-xl hover:transform hover:-translate-y-2">


  <img class="absolute top-8 left-8" v-if="onClickFavorite" @click="onClickFavorite" :src="isFavorite ?  '/like-2.svg': '/like-1.svg'" alt="Favorite" />


  <img :src="imageUrl"  alt="Sneaker" />
  <p>{{ title }}</p>

  <div class="flex justify-between mt-5">
    <div class="flex flex-col gap-2">
      <span class="text-slate-400">Цена:</span>
      <b class="font-bold">{{ price }} руб.</b>
    </div>
    <img v-if="onClickAdd" @click="onClickAdd" :src="!isAdded ? '/plus.svg' : '/checked.svg'" alt="Plus" />

  </div>
</div>
</template>

<style scoped>

</style>