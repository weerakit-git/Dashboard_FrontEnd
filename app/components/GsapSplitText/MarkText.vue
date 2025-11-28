<template>
  <div class="container mx-auto p-5 ">
    <span ref="markRef">Gsap mark split text</span>
  </div>
</template>

<script setup>
import { ref, onMounted ,onUnmounted } from 'vue'
import gsap from 'gsap'
import SplitText from 'gsap/SplitText'

gsap.registerPlugin(SplitText)

const markRef = ref(null)
let split

onMounted(() => {
  const el = markRef.value
  //ป้องกัน wrapper ถูกสร้างซ้ำ
  if (!el.parentNode.classList.contains('mask-wrapper')) {
    const wrapper = document.createElement('div')
    // ป้องกัน blocking 
    wrapper.classList.add('overflow-hidden', 'inline-block') 
    el.parentNode.insertBefore(wrapper, el)
    wrapper.appendChild(el)
  }
  split = new SplitText(el, {type: 'lines'})
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

