<script setup lang="ts">
import type { Product } from '@/App.vue';

const props = defineProps<{
    productData: Product[],
    addQuantity: Function,
}>();

const emit = defineEmits(['toggle-transaction-log'])

</script>

<template>

    <div class="w-full h-full flex flex-col">
        <div class="w-full">
            <div class="relative bg-red-500 py-4 px-6 w-full flex justify-between items-center">
                <h1 class="text-white">Products</h1>

                <div
                    @click="$emit('toggle-transaction-log')" 
                    class="absolute right-5 hover:bg-black/20 w-[32px] h-[32px] cursor-pointer rounded-full flex items-center justify-center transition"
                >
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path fill="#fff" d="M12 2c5.523 0 10 4.477 10 10s-4.477 10-10 10S2 17.523 2 12h2a8 8 0 1 0 1.865-5.135L8 9H2V3l2.447 2.446A9.98 9.98 0 0 1 12 2m1 5v4.585l3.243 3.243l-1.415 1.415L11 12.413V7z"/></svg>
                </div>
            </div>
        </div>
        
        <div class="w-full h-[calc(100vh-4rem)] grid grid-cols-3 gap-3 p-5 overflow-y-scroll">
    
            <div 
                v-for="(product, index) in props.productData" 
                @click="props.addQuantity(index)"
                class="w-full aspect-square flex gap-2 rounded-[10px] overflow-clip border border-gray-200 flex flex-col bg-white hover:brightness-75 hover:border-red-500 transition cursor-pointer"
            >
                <img class="w-full aspect-video object-cover" :src="product.image" alt="">
                <div class="w-full p-3 flex flex-col ">
                    <h2 class="">{{ product.name }}</h2>
                    <h1 class="text-xl">RM {{ (product.price / 100).toFixed(2) }}</h1>
                </div>
            </div>
    
        </div>
    </div>

</template>