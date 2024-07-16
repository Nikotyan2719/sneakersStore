<script setup>
import CartItem from "@/components/CartItem.vue";
import {inject} from "vue";

const {cart, removeFromCart} = inject('cart')

</script>

<template>

  <div class="flex flex-col flex-1 gap-4" v-auto-animate>
    <cart-item v-for="item in cart"
               :key="item.id"
               :title="item.title"
               :price="item.price"
               :image-url="item.imageUrl"
               @onClickRemove = "()=> removeFromCart(item)"/>
  </div>

</template>

<style scoped>

</style>