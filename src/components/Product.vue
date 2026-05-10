<script setup lang="ts">
import type { Product } from '@/App.vue';
import { db } from '@/firebase';
import { addDoc, collection, deleteDoc, doc, getDocs, query, where } from 'firebase/firestore/lite';

const props = defineProps<{
    productData: Product[],
    addQuantity: Function,
    fetchProducts: Function,
}>();

const emit = defineEmits<{
    (e: 'toggle-transaction-log'): void;
    (e: 'toggle-create-product'): void;
    (e: 'toggle-delete-product'): void;
    (e: 'chosenProductName', value: string): void;
}>();

const deleteModal = (name: string) => {
    emit('chosenProductName', name);
    emit('toggle-delete-product');
}

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
        
        <div class="w-full h-[calc(100vh-4rem)] grid grid-cols-3 grid-flow-row gap-3 p-5 overflow-y-scroll">
            <div v-if="props.productData.length === 0" class="text-center py-10 text-gray-400">
                No products found.
            </div>

            <div v-for="(product, index) in props.productData"  class="relative w-full aspect-square rounded-[10px] overflow-clip border border-gray-200 hover:border-red-500">
                <div @click="deleteModal(product.name)" class="absolute z-30 p-1 top-0 right-0 bg-red-500 w-[40px] h-[40px] cursor-pointer rounded-bl-[10px] flex items-center justify-center transition">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24px" height="24px" viewBox="0 0 24 24"><path fill="#fff" d="M17 4h5v2h-2v15a1 1 0 0 1-1 1H5a1 1 0 0 1-1-1V6H2V4h5V2h10zM9 9v8h2V9zm4 0v8h2V9z"/></svg>
                </div>
                <div
                    @click="props.addQuantity(index)"
                    class="relative w-full flex gap-2 flex flex-col bg-white hover:brightness-75 transition cursor-pointer"
                >
                    <img class="w-full aspect-4/3 object-cover" :src="product.image" alt="">
                    <div class="w-full p-3 flex flex-col ">
                        <h2 class="">{{ product.name }}</h2>
                        <h1 class="text-xl">RM {{ (product.price / 100).toFixed(2) }}</h1>
                    </div>
                </div>
            </div>

            <div @click="$emit('toggle-create-product')" class="w-full bg-gray-200 aspect-square flex gap-2 rounded-[10px] overflow-clip border border-gray-200 flex flex-col hover:brightness-75 hover:border-red-500 transition cursor-pointer items-center justify-center">
                <svg xmlns="http://www.w3.org/2000/svg" width="70" height="70" viewBox="0 0 24 24"><path fill="#6b7280" d="M11 11V2h2v9h9v2h-9v9h-2v-9H2v-2z"/></svg>
            </div>
    
        </div>
    </div>

</template>