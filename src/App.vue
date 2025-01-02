<script setup>
import Cart from './components/Cart.vue'
import { ref, onMounted } from 'vue'
import axios from 'axios'

const data = ref([])
const searchQuery = ref()
const osibka = ref(false)
const loading = ref(false)
const error = ref(null)
var pusto = ''
const show = ref(false)

async function fetchData() {
  try {
    loading.value = true
    const response = await axios
      .get(`https://b262209e58923182.mokky.dev/items?title=*${searchQuery.value}`)
      .then(console.log('Zapros na backend prosol uspesno'))
    data.value = response.data
  } catch (err) {
    error.value = err.message
    console.log('Zapros na backend ne prosol uspesno')
    console.log(err)
  } finally {
    loading.value = false
  }

  if (data.value < 1) {
    pusto = searchQuery.value
    osibka.value = true
  } else {
    osibka.value = false
  }

  if (osibka.value) {
    console.log('proizosla osibka v zaprose ocistka cerez 10s')
    setTimeout(() => {
      reset()
    }, 10000)
  }
}

async function reset() {
  const response = await axios.get(`https://b262209e58923182.mokky.dev/items`)
  data.value = response.data
  osibka.value = false
  searchQuery.value = ''
  console.log('obnowleniye towarow proslo uspesno')
}

async function sortDown() {
  show.value = false
  loading.value = true
  const response = await axios.get(`https://b262209e58923182.mokky.dev/items?sortBy=price`)
  loading.value = false
  data.value = response.data
}

async function sortUp() {
  show.value = false
  loading.value = true
  const response = await axios.get(`https://b262209e58923182.mokky.dev/items?sortBy=-price`)
  loading.value = false
  data.value = response.data
}

onMounted(async () => {
  loading.value = true
  const response = await axios
    .get(`https://b262209e58923182.mokky.dev/items?title=*${searchQuery}`)
    .then(console.log('Startowyy Zapros prosol uspesno'))
    .catch(console.log('Startowyy Zapros ne prosol uspesno'))
  data.value = response.data
  loading.value = false
})
</script>

<template>
  <div class="bg-[#F8F7F7] h-full min-h-screen w-full overflow-x-hidden">
    <div class="h-20 bg-white shadow-sm">
      <div class="max-w-[1200px] mx-auto h-20 flex items-center justify-between">
        <div class="flex gap-1 items-center">
          <box-icon name="ghost" color="#6055d8" size="md"></box-icon>
          <div class="text-2xl text-[#6055d8] font-semibold">GShop</div>
        </div>
        <ul class="flex gap-10 text-lg text-[#6055d8] items-center">
          <li class="cursor-pointer">Home</li>
          <li class="cursor-pointer">Shop</li>
          <li class="cursor-pointer">Cart</li>
          <li class="cursor-pointer">About</li>
        </ul>

        <button
          class="px-4 py-2 bg-[#6055d8] text-white rounded-md cursor-pointer items-center hover:scale-105 transition duration-300 ease-in-out"
        >
          Log in
        </button>
      </div>
    </div>

    <div class="max-w-[1200px] mx-auto mt-3">
      <div class="rounded-lg w-full h-full bg-white my-10 p-6 flex gap-6">
        <div class="relative w-full">
          <input
            type="text"
            placeholder="Search"
            class="outline-none border bg-[#F8F7F7] rounded-full w-full p-3.5 pl-10"
            v-model="searchQuery"
            @keyup.enter="fetchData"
          />
          <div class="absolute top-4 left-3">
            <box-icon name="search" color="#5F5D5D"></box-icon>
          </div>
        </div>
        <div class="flex gap-3 min-w-[272.58px] r">
          <div class="h-full relative w-full">
            <button
              type="button"
              class="border bg-[#6055d8] h-full text-white rounded-full py-3 px-10"
              @click="show = !show"
            >
              Sort by
            </button>
            <ul
              v-show="show"
              class="absolute bg-[#6055d8] z-10 rounded-xl p-2 text-white flex flex-col text-center items-center -left-4 top-16 gap-y-3 min-w-fit w-40 justify-center"
            >
              <li
                @click="sortDown"
                class="w-full p-3 hover:bg-[#393281] rounded-full px-10 transition duration-150 cursor-pointer flex items-center justify-center gap-3"
              >
                Price <box-icon name="sort-down" color="#ffffff"></box-icon>
              </li>
              <li
                @click="sortUp"
                class="p-3 hover:bg-[#393281] rounded-full px-10 transition duration-150 cursor-pointer flex items-center justify-center gap-3"
              >
                Price <box-icon name="sort-up" color="#ffffff"></box-icon>
              </li>
            </ul>
          </div>
          <button
            @click="fetchData"
            :disabled="!searchQuery"
            class="py-3 px-10 bg-[#6055d8] text-white rounded-full font-semibold disabled:bg-[#393281] transition duration-150"
          >
            Search
          </button>
        </div>
      </div>
      <div class="text-2xl text-[#6055d8] font-semibold text-center mb-10" v-show="loading">
        loading...
      </div>
      <h1 v-show="osibka" class="text-2xl font-semibold text-center mb-10 text-red-500">
        товаров по запросу {{ pusto }} не найдено
      </h1>
      <div
        class="grid grid-cols-3 gap-3 xl:grid-cols-3 laptop:grid-cols-3 tablet:grid-cols-2 mobile:grid-cols-1"
        v-if="!loading"
      >
        <Cart v-for="(obj, index) in data" :key="index" :data="obj" />
      </div>
    </div>
  </div>
</template>

<style scoped></style>
