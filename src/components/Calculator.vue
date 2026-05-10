<script lang="ts" setup>
import { computed } from 'vue';
import type { Product } from '@/App.vue';

const props = defineProps<{
    productData: Product[],
    discount: number,
    addQuantity: Function,
    subtractQuantity: Function,
    setDiscount: Function,
}>();

const emit = defineEmits(['open-checkout']);

const subtotal = computed(() => {
    return props.productData.reduce((acc, item) => acc + (item.price * item.quantity), 0);
});

const finalTotal = computed(() => {
    const multiplier = 1 - props.discount;
    return (subtotal.value * multiplier) / 100;
});

defineExpose({
    finalTotal,
    subtotal,
});
</script>

<template>
    <div class="w-full h-full flex flex-col">
        <div class="w-full grow">
            <div class="bg-red-700 py-4 px-6">
                <h1 class="text-white font-bold">Chosen Items:</h1>
            </div>

            <div class="w-full h-[calc(100vh-30rem)] flex flex-col overflow-y-scroll">
                <template v-for="(product, index) in props.productData" :key="index">
                    <div v-if="product.quantity > 0" class="w-full h-[60px] flex gap-2 items-center px-5 border-gray-300 border-b">
                        <div class="grow h-full p-2 flex gap-3 items-center">
                            <img class="h-full aspect-square object-cover rounded-[5px]" :src="product.image">
                            <h2 class="text-sm font-medium">{{ product.name }}</h2>
                        </div>

                        <div class="flex bg-gray-100 h-8 w-24 rounded-[6px] overflow-clip border border-gray-200">
                            <button @click="props.subtractQuantity(index)" class="flex-1 hover:bg-red-100 transition">-</button>
                            <div class="flex-1 flex items-center justify-center font-bold">{{ product.quantity }}</div>
                            <button @click="props.addQuantity(index)" class="flex-1 hover:bg-green-100 transition">+</button>
                        </div>
                        <h1 class="w-20 text-right font-semibold">RM {{ ((product.price * product.quantity) / 100).toFixed(2) }}</h1>
                    </div>
                </template>
            </div>
        </div>

        <!-- Discount Selectors -->
        <div class="w-full h-[50px] flex-none flex cursor-pointer border-t border-gray-200">
            <button v-for="d in [0.25, 0.50, 0.75]" 
                @click="props.setDiscount(d)"
                :class="['w-1/3 h-full flex items-center justify-center transition text-sm font-bold border-r border-gray-200', 
                props.discount === d ? 'bg-red-600 text-white' : 'bg-red-50 hover:bg-red-100']">
                -{{ d * 100 }}%
            </button>
        </div>

        <!-- Final Calculation Display -->
        <div class="flex-none w-full bg-gray-50 border-t border-gray-300 py-8 px-7 text-right">
            <p v-if="props.discount > 0" class="text-sm text-red-600 line-through">Subtotal: RM {{ (subtotal / 100).toFixed(2) }}</p>
            <h1 class="text-5xl font-bold">RM {{ finalTotal.toFixed(2) }}</h1>
        </div>

        <div @click="emit('open-checkout')" class="w-full h-[100px] flex-none flex cursor-pointer bg-red-700 text-white items-center justify-center gap-3 text-2xl hover:bg-red-800 transition uppercase font-black">
            <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24"><path fill="currentColor" d="M3 4.003h18a1 1 0 0 1 1 1v14a1 1 0 0 1-1 1h-18a1 1 0 0 1-1-1v-14a1 1 0 0 1 1-1M6.5 6H4v2.5A2.5 2.5 0 0 0 6.5 6m11 0A2.5 2.5 0 0 0 20 8.5V6zM4 15.5V18h2.5A2.5 2.5 0 0 0 4 15.5M17.5 18H20v-2.5a2.5 2.5 0 0 0-2.5 2.5M12 16a4 4 0 1 0 0-8a4 4 0 0 0 0 8"/></svg>
            Checkout
        </div>
    </div>
</template>