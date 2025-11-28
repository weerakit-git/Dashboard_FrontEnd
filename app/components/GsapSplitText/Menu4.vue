
<template>
  <div class="bg-red-200 w-[calc(100%-4em)] mx-auto p-2 relative rounded-4xl">

    <div class="flex justify-between items-center">
      <div>LOGO</div>
      <img 
        src="/menu.svg" 
        alt="" 
        class="w-8 h-8 cursor-pointer"
        @click="toggleMenu"
      >
    </div>

    <!-- MENU WRAPPER -->
    <div ref="menuWrapper" class="overflow-hidden ">
      <ul ref="menuContent">
        <li 
          v-for="(item, index) in items" 
          :key="index"
          class="overflow-hidden"
        >
          <span class="menu-line block text-[40px] font-bold leading-none">
            {{ item }}
          </span>
        </li>
      </ul>
    </div>

  </div>
</template>
<script setup>
import { ref, watch, nextTick, onMounted } from 'vue'
import gsap from 'gsap'
import SplitText from 'gsap/SplitText'
gsap.registerPlugin(SplitText)

const isOpen = ref(false)
const items = ['Home', 'Contact', 'Project', 'Work']

const menuWrapper = ref(null)
const menuContent = ref(null)
let split = null

const toggleMenu = () => {
  isOpen.value = !isOpen.value
}

onMounted(() => {
  gsap.set(menuWrapper.value, { height: 0 })
})

watch(isOpen, async (val) => {
  await nextTick()

  // ---------------------------------------
  // ❌ CLOSE MENU
  // ---------------------------------------
  if (!val) {

    if (split) {
      gsap.timeline()
        .to(split.lines, {
          yPercent: 110,
          opacity: 0,
          duration: 0.25,
          stagger: 0.05,
          ease: "power2.in"
        })

        // 1) ซ่อนเมนูก่อน revert
        .set(menuContent.value, { opacity: 0 })

        // 2) revert ได้อย่างปลอดภัย — ไม่มี text กระพริบ
        .add(() => split.revert())

        // 3) ค่อยปิด wrapper
        .to(menuWrapper.value, {
          height: 0,
          duration: 0.35,
          ease: "power2.inOut"
        })
    } 
    else {
      gsap.to(menuWrapper.value, {
        height: 0,
        duration: 0.35
      })
    }

    return
  }

  // ---------------------------------------
  // ✅ OPEN MENU
  // ---------------------------------------

  split?.revert()
  split = new SplitText(menuContent.value.querySelectorAll('.menu-line'), { type: "lines" })

  const fullHeight = menuContent.value.scrollHeight

  gsap.timeline()
    .set(menuContent.value, { opacity: 1 })   // ให้ text พร้อมถูก animate
    .to(menuWrapper.value, {
      height: fullHeight,
      duration: 0.4,
      ease: "power2.out"
    })
    .from(split.lines, {
      yPercent: 110,
      opacity: 0,
      duration: 0.45,
      stagger: 0.07,
      ease: "power2.out"
    }, "-=0.15")
})
</script>
