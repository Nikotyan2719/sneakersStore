<script setup>
import DrawerHead from "@/components/DrawerHead.vue";
import CartItemList from "@/components/CartItemList.vue";
import InfoBlock from "@/components/InfoBlock.vue";
import axios from "axios";
import {computed, inject, ref} from "vue";

const emit = defineEmits(['createOrder'])


const props = defineProps({
  vatPrice: Number,
  totalPrice: Number,
})


const {cart} = inject('cart')

const isCreating = ref(false)
const orderId = ref(null)

const createOrder = async () =>{
  try {
    isCreating.value = true
    const {data} = await axios.post("https://b7ed7504a1815ece.mokky.dev/orders", {
      items: cart.value,
      totalPrice: props.totalPrice.value
    })

    cart.value = []

    orderId.value = data.id
  } catch (err){
    console.log(err)
  } finally {
    isCreating.value = false
  }
}

const cartIsEmpty = computed(()=> cart.value.length===0);
const ButtonDisabled = computed(()=> isCreating.value || cartIsEmpty.value)
</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-10">
    <drawer-head/>

    <div v-if="!totalPrice || orderId" class="flex h-full items-center">
      <InfoBlock v-if="!totalPrice && !orderId" title="Корзина пустая" description="Добавьте что-нибудь" image-url="/package-icon.png"/>
      <InfoBlock v-if="orderId" title="Заказ оформлен!" :description="`Ваш заказ №${orderId} будет доставлен`" image-url="/order-success-icon.png"/>
    </div>

    <div v-else>
      <cart-item-list/>

      <div v-if="totalPrice" class="flex-col gap-4 my-7 mt-7">
        <div class="flex gap-2">
          <span>Итого:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ totalPrice }} руб.</b>
        </div>
        <div class="flex gap-2">
          <span>Налог 5%:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ vatPrice }} руб.</b>
        </div>
        <button @click="createOrder"
                :disabled="ButtonDisabled"
                class="mt-4 transition bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-700 disabled:bg-slate-300 cursor-pointer">
          Оформить заказ</button>
      </div>
    </div>


  </div>

</template>

<style scoped>

</style>