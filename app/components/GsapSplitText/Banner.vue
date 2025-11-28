<template>
  <div>
    <h1>
      <div class="overflow-hidden">
        <span class="line text-[50px] leading-none">DIGITAL</span>
      </div>

      <div class="overflow-hidden">
        <span class="line text-[50px] leading-none">CREATIVE</span>
      </div>

      <div class="overflow-hidden">
        <span class="line text-[50px] leading-none">MARKETING</span>
      </div>
    </h1>
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
    duration: 0.6,
    ease: 'power2.out',
    stagger: 0.08,
    delay: 0.2
  })
})

onUnmounted(() => {
  split?.revert()
})
</script>

