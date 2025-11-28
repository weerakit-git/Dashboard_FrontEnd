<template>
  <div class="container mx-auto p-5">
    <div class="overflow-hidden inline-block">
      <span class="line">Gsap mark split text</span>
    </div>
  </div>
</template>
<script setup>
import { onMounted, onUnmounted } from 'vue'
import gsap from 'gsap'
import SplitText from 'gsap/SplitText'

gsap.registerPlugin(SplitText)

let split

onMounted(() => {
  split = new SplitText('.line', { type: 'lines' })
  gsap.from(split.lines, {
    yPercent: 110,
    duration: 0.5,
    ease: 'power2.out',
    stagger: 0.05,
    delay: 0.5
  })
})

onUnmounted(() => {
  split?.revert()
})
</script>
