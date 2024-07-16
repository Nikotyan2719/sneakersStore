<script setup>
import CardList from "@/components/cardList.vue";
import axios from "axios";
import {inject, onMounted, reactive, ref, watch} from "vue";
import debounce from "lodash.debounce"

const {addToCart, removeFromCart, cart} = inject('cart')

const items = ref([])

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})

const onClickAddPlus = item =>{
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}
const addToFavorites = async  (item) =>{
  try {
    if (!item.isFavorite){
      const obj = {
        item_id: item.id
      }
      item.isFavorite = true
      const {data} = await axios.post("https://b7ed7504a1815ece.mokky.dev/favorites", obj)
      item.favoriteId = data.id
    }else {
      item.isFavorite = false
      await axios.delete(`https://b7ed7504a1815ece.mokky.dev/favorites/${item.favoriteId}`)
      item.favoriteId = null
    }
  } catch (err){
    console.log(err)
  }
}

const onChangeSelect = event =>{
  filters.sortBy = event.target.value;
}
const onChangeSearchInput = debounce(event =>{
  filters.searchQuery = event.target.value;
}, 500)

const fetchFavorites = async ()=>{
  try {

    const {data: favorites} = await axios.get("https://b7ed7504a1815ece.mokky.dev/favorites")
    items.value = items.value.map(item =>{
      const favorite = favorites.find(favorite => favorite.item_id === item.id)

      if (!favorite){
        return item;
      }

      return{
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })

  } catch (e){
    console.log(e)
  }
}

const fetchItems = async () =>{
  try {
    const params = {
      sortBy: filters.sortBy,
    }
    if (filters.searchQuery){
      params.title = `*${filters.searchQuery}*`
    }
    const {data} = await axios.get("https://b7ed7504a1815ece.mokky.dev/items", {
      params
    })
    items.value = data.map(obj =>({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (e){
    console.log(e)
  }
}

onMounted(async ()=>{
  const localCart = localStorage.getItem('cart')
  if (localCart) {
    cart.value = JSON.parse(localCart)
  } else {
    cart.value = []
  }


  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item)=>({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

watch(cart, () =>{
  items.value = items.value.map((item)=>({
    ...item,
    isAdded: false
  }))
})

watch(filters, fetchItems)

</script>

<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>
    <div class="flex gap-4">
      <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
        <option value="name">По названию</option>
        <option value="price">По цене(дешевые)</option>
        <option value="-price">По цене(дорогие)</option>
      </select>
      <div class="relative">
        <img src="/search.svg" alt="Поиск" class="absolute left-4 top-3">
        <input @input="onChangeSearchInput" type="text" placeholder="Поиск..." class="border rounded-nd py-2 pl-11 pr-4 outline-none focus:border-gray-400"/>
      </div>
    </div>
  </div>

  <div class="mt-10">
    <card-list :items="items" @add-to-favorite="addToFavorites" @add-to-cart="onClickAddPlus"/>
  </div>
</template>

<style scoped>

</style>