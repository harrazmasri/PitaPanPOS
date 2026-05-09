<script setup lang="ts">
import { computed } from 'vue';
import type { Product } from '@/App.vue';

const props = defineProps<{
    productData: Product[],
    discount: number
}>();

defineEmits(['close']);

const finalTotal = computed(() => {
    const subtotal = props.productData.reduce((acc, item) => acc + (item.price * item.quantity), 0);
    return ((subtotal * (1 - props.discount)) / 100).toFixed(2);
});
</script>

<template>
    <div class="fixed top-0 left-0 z-50 w-screen h-screen bg-black/60 backdrop-blur-sm flex justify-center items-center gap-7">
        <!-- Close Button -->
        <div @click="$emit('close')" class="absolute z-[51] top-5 right-5 hover:bg-white/20 w-[50px] h-[50px] cursor-pointer rounded-full flex items-center justify-center transition">
            <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24"><path fill="#fff" d="M10.586 12L2.793 4.207l1.414-1.414L12 10.586l7.793-7.793l1.414 1.414L13.414 12l7.793 7.793l-1.414 1.414L12 13.414l-7.793 7.793l-1.414-1.414z"/></svg>
        </div>

        <div class="w-fit h-fit flex flex-col gap-4">
            <div class="w-[400px] h-[500px] bg-white flex flex-col rounded-[20px] shadow-2xl overflow-clip">
                <div class="flex-none bg-red-600 py-4 px-6 text-white">
                    <h1 class="text-xl font-bold">Order Summary</h1>
                </div>

                <div class="grow overflow-y-auto">
                    <template v-for="product in props.productData">
                        <div v-if="product.quantity > 0" class="flex justify-between items-center p-4 border-b border-gray-100">
                            <div>
                                <p class="font-bold text-gray-800">{{ product.name }}</p>
                                <p class="text-xs text-gray-500">{{ product.quantity }}x @ RM {{ (product.price / 100).toFixed(2) }}</p>
                            </div>
                            <p class="font-mono">RM {{ ((product.price * product.quantity) / 100).toFixed(2) }}</p>
                        </div>
                    </template>
                </div>

                <div class="bg-gray-50 p-6">
                    <div v-if="discount > 0" class="flex justify-between text-red-600 text-sm mb-2">
                        <span>Discount applied:</span>
                        <span>-{{ discount * 100 }}%</span>
                    </div>
                    <div class="flex justify-between items-center pt-2 border-t border-gray-200">
                        <span class="text-lg font-bold">Total</span>
                        <span class="text-3xl font-black">RM {{ finalTotal }}</span>
                    </div>
                </div>
            </div>
            
            <div class="w-full flex gap-3">
                <button @click="$emit('close')" class="w-1/2 p-4 rounded-xl bg-gray-200 font-bold">Failed</button>
                <button class="w-1/2 p-4 rounded-xl bg-green-600 text-white font-bold">Success</button>
            </div>
        </div>
        
        <!-- QR Panel -->
        <div class="w-[300px] bg-white rounded-[20px] shadow-2xl overflow-clip">
             <div class="bg-red-600 py-4 px-6 text-white text-center font-bold">DuitNow QR</div>
             <div class="pt-2 pb-8 px-2">
                <div class="w-full bg-gray-100 rounded-lg flex items-center justify-center border-2 border-dashed border-gray-300 ">
                    <img src="/public/qr_harraz.jpeg" alt="">
                </div>
                <p class="text-center mt-4 text-xs text-gray-400">Scan to pay RM {{ finalTotal }}</p>
             </div>
        </div>
    </div>
</template>