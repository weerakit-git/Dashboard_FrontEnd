<template>
  <div
    ref="headerRef"
    class="fixed top-0 left-0 w-full flex justify-center items-center mt-5 z-50 header-wrapper"
  >
    <div
      class="bg-black/10 backdrop-blur-sm border border-white/20 
      w-[calc(100%-2em)] md:w-[calc(100%-18em)] mx-auto py-2 px-3 relative rounded-3xl"
    >
      <div class="flex justify-between items-center">
        <div class="text-xl font-bold text-white">
          <a href="/">Digtal Nation Agency</a>
        </div>

        <img
          src="/menu.svg"
          class="w-8 h-8 cursor-pointer block xl:hidden"
          @click="toggleMenu"
        />

        <!-- Desktop Menu -->
        <div class="hidden xl:block">
          <ul>
            <li
              v-for="(item, index) in items"
              :key="index"
              class="hidden md:inline-block ml-6 text-lg font-medium cursor-pointer"
            >
              <a :href="item.url">{{ item.label }}</a>
            </li>
          </ul>
        </div>

        <!-- Social -->
        <div class="hidden xl:flex space-x-4">
          <img src="/fb.png" class="w-8 h-8 object-contain" />
          <img src="/line.png" class="w-8 h-8 object-contain" />
          <img src="/ig.png" class="w-8 h-8 object-contain" />
        </div>
      </div>

      <!-- MOBILE MENU -->
      <div ref="menuWrapper" class="overflow-hidden h-0 opacity-0 block xl:hidden">
        <ul ref="menuContent" class="pt-3">
          <li
            v-for="(item, index) in items"
            :key="index"
            class="overflow-hidden"
          >
            <a :href="item.url" class="block">
              <span
                class="menu-line block text-[25px] font-medium leading-[1.4] ml-4"
              >
                {{ item.label }}
              </span>
            </a>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, nextTick, onMounted, onUnmounted } from "vue";
import gsap from "gsap";
import SplitText from "gsap/SplitText";

gsap.registerPlugin(SplitText);

// Menu State
const isOpen = ref(false);

const items = [
  { label: "Works", url: "/works" },
  { label: "Services", url: "/services" },
  { label: "Knowledge", url: "/knowledge" },
  { label: "Contact", url: "/contact" }
];

const headerRef = ref(null);
const menuWrapper = ref(null);
const menuContent = ref(null);

let split = null;

// Timeline objects
let menuTL = gsap.timeline({ paused: true });
let scrollTL = gsap.timeline({ paused: true });

// Toggle
const toggleMenu = () => {
  isOpen.value = !isOpen.value;

  // เปิดเมนู → ทำให้ Header แสดงทันที
  if (isOpen.value) {
    gsap.to(headerRef.value, {
      y: 0,
      scale: 1,
      opacity: 1,
      duration: 0.25,
      ease: "power2.out"
    });
  }
};

// ---------------------------------------------
// Mounted
// ---------------------------------------------
onMounted(() => {
  gsap.set(menuWrapper.value, { height: 0, opacity: 0 });

  setupScrollHideHeader();
});

// Cleanup scroll listener
onUnmounted(() => {
  window.removeEventListener("scroll", scrollHandler);
});

// ---------------------------------------------
// Menu Watcher
// ---------------------------------------------
watch(isOpen, async (val) => {
  await nextTick();
  menuTL.clear();

  if (!val) closeMenu();
  else openMenu();
});

// ---------------------------------------------
// OPEN MENU
// ---------------------------------------------
const openMenu = () => {
  split?.revert();

  split = new SplitText(
    menuContent.value.querySelectorAll(".menu-line"),
    { type: "lines" }
  );

  const fullHeight = menuContent.value.scrollHeight || 200;

  menuTL
    .set(menuContent.value, { opacity: 1 })
    .to(menuWrapper.value, { opacity: 1, duration: 0.1 })
    .to(menuWrapper.value, {
      height: fullHeight,
      duration: 0.4,
      ease: "power2.out"
    })
    .from(
      split.lines,
      {
        yPercent: 110,
        opacity: 0,
        duration: 0.45,
        stagger: 0.07,
        ease: "power2.out"
      },
      "-=0.15"
    );

  menuTL.play();
};

// ---------------------------------------------
// CLOSE MENU
// ---------------------------------------------
const closeMenu = () => {
  if (!split) {
    menuTL.to(menuWrapper.value, { height: 0, opacity: 0, duration: 0.35 });
    menuTL.play();
    return;
  }

  menuTL
    .to(split.lines, {
      yPercent: 110,
      opacity: 0,
      duration: 0.25,
      stagger: 0.05,
      ease: "power2.in"
    })
    .set(menuContent.value, { opacity: 0 })
    .add(() => split.revert())
    .to(menuWrapper.value, {
      height: 0,
      opacity: 0,
      duration: 0.35,
      ease: "power2.inOut"
    });

  menuTL.play();
};

// ---------------------------------------------
// SCROLL HIDE HEADER
// ---------------------------------------------
let scrollHandler;
function setupScrollHideHeader() {
  const header = headerRef.value;

  let prevScroll = window.scrollY;
  let isHidden = false;

  scrollTL = gsap.timeline({ paused: true })
    .to(header, { duration: 0.2, ease: "power2.out" })
    .to(header, { y: -80, opacity: 0, duration: 0.35, ease: "power3.inOut" });

 scrollHandler = () => {
  if (isOpen.value) return;

  const current = window.scrollY;
  const diff = Math.abs(current - prevScroll);

  // NEW — ถ้าชนบนสุด ให้ header กลับมาทันที
  if (current <= 0) {
    scrollTL.reverse();
    isHidden = false;
    prevScroll = 0;
    return;
  }

  if (diff < 6) return;

  if (current > prevScroll && !isHidden) {
    scrollTL.play();
    isHidden = true;
  } else if (current < prevScroll && isHidden) {
    scrollTL.reverse();
    isHidden = false;
  }

  prevScroll = current;
};


  window.addEventListener("scroll", scrollHandler);
}
</script>
