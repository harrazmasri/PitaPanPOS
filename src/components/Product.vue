<script setup lang="ts">
import type { Product } from '@/App.vue';

const props = defineProps<{
    productData: Product[],
    addQuantity: Function,
}>();

</script>

<template>

    <div class="w-full h-full flex flex-col">
        <div class="w-full">
            <div class="bg-red-500 py-4 px-6">
                <h1 class="text-white">Products</h1>
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