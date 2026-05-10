<script setup lang="ts">
import { onMounted, ref } from 'vue';

const props = defineProps<{
    isTransactionSuccess: boolean,
    resetQuantity: Function,
}>();

const emit = defineEmits(['close-success']);

const countdown = ref(15);

onMounted(() => {
    props.resetQuantity();
    
    setInterval(() => {
        if (countdown.value > 0) countdown.value--;

        if (countdown.value === 0) {
            emit('close-success')
        }
    }, 1000);
});

</script>

<template>

<div class="fixed top-0 left-0 z-[52] w-screen h-screen bg-black/60 backdrop-blur-sm flex justify-center items-center gap-7">
    <!-- Close Button -->
    <div @click="$emit('close-success')" class="absolute z-[53] top-5 right-5 hover:bg-white/20 w-[50px] h-[50px] cursor-pointer rounded-full flex items-center justify-center transition">
        <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24"><path fill="#fff" d="M10.586 12L2.793 4.207l1.414-1.414L12 10.586l7.793-7.793l1.414 1.414L13.414 12l7.793 7.793l-1.414 1.414L12 13.414l-7.793 7.793l-1.414-1.414z"/></svg>
    </div>

    <div class="w-fit h-fit flex flex-col items-center justify-center gap-4">
        <div v-if="isTransactionSuccess" class="flex flex-col justify-center items-center">
            <svg xmlns="http://www.w3.org/2000/svg" width="120" height="120" viewBox="0 0 24 24"><path fill="#fff" d="m11.602 13.76l1.412 1.412l8.466-8.466l1.414 1.415l-9.88 9.88l-6.364-6.365l1.414-1.414l2.125 2.125zm.002-2.828l4.952-4.953l1.41 1.41l-4.952 4.953zm-2.827 5.655L7.364 18L1 11.636l1.414-1.414l1.413 1.413l-.001.001z"/></svg>
            <h1 class="text-3xl font-bold text-white">Success</h1>
        </div>
        <div v-else class="flex flex-col justify-center items-center">
            <svg xmlns="http://www.w3.org/2000/svg" width="120" height="120" viewBox="0 0 24 24"><path fill="#fff" d="M10.586 12L2.793 4.207l1.414-1.414L12 10.586l7.793-7.793l1.414 1.414L13.414 12l7.793 7.793l-1.414 1.414L12 13.414l-7.793 7.793l-1.414-1.414z"/></svg>
            <h1 class="text-3xl font-bold text-white">Failed</h1>
        </div>
        <p class="text-white">closing in {{ countdown }}s</p>
    </div>
</div>

</template>