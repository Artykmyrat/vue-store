<template>
  <div class="relative overflow-hidden bg-white rounded-xl p-4 pb-20 h-full">
    <div class="min-h-[250px] flex justify-center items-center">
      <img
        :src="data.image"
        class="scale-105 w-1/2 h-full max-h-[250px] mx-auto"
        alt="loading..."
      />
    </div>
    <div class="mt-5">
      <h1 class="text-2xl font-semibold">{{ data.title }}</h1>
    </div>
    <div class="flex justify-between absolute bottom-3 w-full pr-10">
      <div class="mt-2 text-3xl text-[#6055d8] font-semibold">{{ data.price }}$</div>
      <button
        class="p-2 bg-[#6055d8] flex items-center justify-center rounded-md cursor-pointer hover:scale-105 transition duration-300"
      >
        <box-icon type="solid" name="cart-alt" color="#fff"></box-icon>
      </button>
    </div>
  </div>
</template>

<script setup>
import 'boxicons'
defineProps({
  data: Array,
})
</script>
