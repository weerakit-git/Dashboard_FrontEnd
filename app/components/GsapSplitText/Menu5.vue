<template>
    <div
        class="bg-black/10 backdrop-blur-sm border border-white/20 w-[calc(100%-2em)] md:w-[calc(100%-18em)] mx-auto py-2 px-3 relative rounded-3xl">
        <div class="flex justify-between items-center">
            <div class="text-2xl font-bold">Digtal Nation Agency</div>
            <img src="/menu.svg" class="w-8 h-8 cursor-pointer block md:hidden" @click="toggleMenu">
            <!-- menu desktop -->
            <div class="hidden md:block">
                <ul>
                    <li v-for="(item, index) in items" :key="index"
                        class="hidden md:inline-block ml-6 text-lg font-medium cursor-pointer">
                        <a :href="item.url">{{ item.label }}</a>
                    </li>
                </ul>
            </div>
            <div class="hidden md:flex space-x-4">
                <img src="/fb.png" alt="icon" class="w-8 h-8 object-contain">
                <img src="/line.png" alt="icon" class="w-8 h-8 object-contain">
                <img src="/ig.png" alt="icon" class="w-8 h-8 object-contain">
            </div>
        </div>
        <!-- menu mobile -->
        <div ref="menuWrapper" class="overflow-hidden h-0 opacity-0 block md:hidden">
            <ul ref="menuContent" class="pt-3">
                <li v-for="(item, index) in items" :key="index" class="overflow-hidden">
                    <a :href="item.url" class="block">
                        <span class="menu-line block text-[25px] font-medium leading-[1.4] ml-4">
                            {{ item.label }}
                        </span>
                    </a>
                </li>
            </ul>
        </div>
    </div>
</template>


<script setup>
import { ref, watch, nextTick, onMounted } from "vue";
import gsap from "gsap";
import SplitText from "gsap/SplitText";

gsap.registerPlugin(SplitText);

const isOpen = ref(false);
const items = [
    { label: "Works", url: "/works" },
    { label: "Services", url: "/services" },
    { label: "Knowledge", url: "/knowledge" },
    { label: "Contact", url: "/contact" }
];

const menuWrapper = ref(null);
const menuContent = ref(null);

let split = null;
let tl = gsap.timeline({ paused: true });

const toggleMenu = () => {
    isOpen.value = !isOpen.value;
};

onMounted(() => {
    gsap.set(menuWrapper.value, { height: 0, opacity: 0 });
});

watch(isOpen, async (val) => {
    await nextTick();
    tl.clear();      // ล้าง timeline เดิม เพื่อไม่ให้ animation ซ้อนกัน

    if (!val) closeMenu();
    else openMenu();
});

const closeMenu = () => {
    if (!split) {
        tl.to(menuWrapper.value, { height: 0, opacity: 0, duration: 0.35 });
        tl.play();
        return;
    }

    tl.to(split.lines, {
        yPercent: 110,
        opacity: 0,
        duration: 0.25,
        stagger: 0.05,
        ease: "power2.in",
    })
        .set(menuContent.value, { opacity: 0 })
        .add(() => split.revert())
        .to(menuWrapper.value, {
            height: 0,
            opacity: 0,
            duration: 0.35,
            ease: "power2.inOut",
        });

    tl.play();
};

const openMenu = () => {
    split?.revert();

    split = new SplitText(
        menuContent.value.querySelectorAll(".menu-line"),
        { type: "lines" }
    );

    const fullHeight = menuContent.value.scrollHeight || 200;

    tl.set(menuContent.value, { opacity: 1 })
        .to(menuWrapper.value, { opacity: 1, duration: 0.1 })
        .to(menuWrapper.value, {
            height: fullHeight,
            duration: 0.4,
            ease: "power2.out",
        })
        .from(split.lines, {
            yPercent: 110,
            opacity: 0,
            duration: 0.45,
            stagger: 0.07,
            ease: "power2.out",
        }, "-=0.15");

    tl.play();
};
</script>
