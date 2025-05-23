<script setup lang="ts">
import { onMounted, ref, useTemplateRef } from 'vue';
import { motion } from 'motion-v';

const props = defineProps<{
    tabs: string[],
    currentTab: string
}>();

const emit = defineEmits<{
    (e: 'update-tab', newTab: string): void
}>();

const tabRefs = useTemplateRef<(HTMLButtonElement)[]>('tab');
const highlightX = ref<number>(0);
const highlightWidth = ref<string>('0px');

onMounted(() => {
    const btn = tabRefs.value?.[0];
    if (btn) {
        highlightWidth.value = `${btn.offsetWidth}px`;
        highlightX.value = btn.offsetLeft;
    }
});

const handleClick = (tab: string, i: number) => {
    emit('update-tab', tab);

    const btn = tabRefs.value?.[i];
    if (btn) {
        highlightWidth.value = `${btn.offsetWidth}px`;
        highlightX.value = btn.offsetLeft;

        btn.scrollIntoView({behavior: 'smooth'});
    }
}
</script>

<template>
    <div class="relative w-fit h-auto">
        <!-- Highlight -->
        <motion.div 
            class="absolute h-full -z-10 bg-sky-600 rounded-xl shadow-md" 
            :style="{ width: highlightWidth }"
            :animate="{ x: highlightX }"
            :transition="{ duration: 0.3, ease: 'easeInOut' }"
        />
        <!-- Button -->
        <div class="flex shadow-lg rounded-xl overflow-hidden">
            <button
                v-for="(tab, i) in props.tabs"
                ref="tab"
                class="relative font-semibold mx-1 px-4 py-2 cursor-pointer transition-colors duration-300 rounded-lg"
                :class="currentTab === tab ? 'text-white' : 'text-slate-800 hover:text-sky-600'"
                @click.prevent="handleClick(tab, i)"
            >
                {{ tab }}
            </button>
        </div>
    </div>
</template>
