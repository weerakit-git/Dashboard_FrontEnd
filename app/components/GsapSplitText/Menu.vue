<template>
  <div class="flex justify-between items-center w-[calc(100%-4em)] bg-amber-400 mx-auto">
    <div>LOGO</div>
    <img src="/menu.svg" alt="" class="w-8 h-8 cursor-pointer" @click="isOpen = !isOpen">
  </div>
  <div v-if="isOpen">
    <ul>
        <li class="text-[40px] font-bold"><a href="#">Home</a></li>
        <li class="text-[40px] font-bold"><a href="#">Contact</a></li>
        <li class="text-[40px] font-bold"><a href="#">Project</a></li>
        <li class="text-[40px] font-bold"><a href="#">Work</a></li>
    </ul>
  </div>
</template>
<script setup>
import { ref } from 'vue'
const isOpen = ref(false)
</script>

