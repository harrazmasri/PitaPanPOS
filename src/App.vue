<script setup lang="ts">
import { ref, computed } from 'vue';
import ProductComp from './components/Product.vue';
import Calculator from './components/Calculator.vue';
import Checkout from './components/Checkout.vue';

export interface Product {
	image: string,
	name: string,
	price: number, // cents
	quantity: number,
}

const productData = ref<Product[]>([
	{
		image: 'https://thecookingfoodie.com/wp-content/uploads/2025/11/viral-doner-kebab-recipe-500x375.jpg',
		name: 'Kebab Biasa',
		price: 600,
		quantity: 0,
	},
	{
		image: 'https://thecookingfoodie.com/wp-content/uploads/2025/11/viral-doner-kebab-recipe-500x375.jpg',
		name: 'Kebab Special',
		price: 1200,
		quantity: 0,
	},
]);

const discount = ref(0); // 0.25, 0.50 etc.
const isCheckoutOpen = ref(false);

const addQuantity = (index: number) => {
	const product = productData.value[index];
	if (product) {
		product.quantity++;
	}
}

const subtractQuantity = (index: number) => {
	const product = productData.value[index];
	if (product && product.quantity > 0) {
		product.quantity--;
	}
}

const setDiscount = (val: number) => {
	// If clicking the same discount again, toggle it off
	discount.value = discount.value === val ? 0 : val;
}

const toggleCheckout = () => {
	isCheckoutOpen.value = !isCheckoutOpen.value;
}
</script>

<template>
	<div class="relative w-screen h-screen flex">
		<div class="w-3/5">
			<ProductComp :productData="productData" :addQuantity="addQuantity" />
		</div>
		<div class="w-2/5 border-red-700 border-l h-full">
			<Calculator :productData="productData" :discount="discount" :addQuantity="addQuantity"
				:subtractQuantity="subtractQuantity" :setDiscount="setDiscount" @open-checkout="toggleCheckout" />
		</div>
	</div>

	<Checkout v-if="isCheckoutOpen" :productData="productData" :discount="discount" @close="toggleCheckout" />
</template>