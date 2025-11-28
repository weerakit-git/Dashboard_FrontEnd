<template>
  <div class="bg-red-200 w-[calc(100%-4em)] mx-auto p-2 relative rounded-4xl">

    <!-- Navbar -->
    <div class="flex justify-between items-center">
      <div>LOGO</div>
      <img 
        src="/menu.svg" 
        alt="" 
        class="w-8 h-8 cursor-pointer"
        @click="toggleMenu"
      >
    </div>

    <!-- MENU -->
    <div v-if="isOpen" class="mt-4">
      <ul>
        <li v-for="(item, index) in items" :key="index" class="overflow-hidden">
          <span class="menu-line block text-[40px] font-bold leading-none">
            {{ item }}
          </span>
        </li>
      </ul>
    </div>

  </div>
</template>

<script setup>
import { ref, watch, nextTick, onUnmounted } from 'vue'
import gsap from 'gsap'
import SplitText from 'gsap/SplitText'

gsap.registerPlugin(SplitText)

const isOpen = ref(false)
const items = ['Home', 'Contact', 'Project', 'Work']

let split

const toggleMenu = () => {
  isOpen.value = !isOpen.value
}

// ✅ เมื่อ isOpen เปลี่ยน → Trigger animation
watch(isOpen, async (val) => {
  if (val) {
    await nextTick()

    // Split ทุกบรรทัดของเมนู
    split = new SplitText('.menu-line', { type: 'lines' })

    gsap.from(split.lines, {
      yPercent: 110,
      duration: 0.5,
      ease: 'power2.out',
      stagger: 0.08
    })
  } else {
    split?.revert()
  }
})

// cleanup
onUnmounted(() => {
  split?.revert()
})
</script>
