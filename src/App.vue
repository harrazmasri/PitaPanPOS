<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import ProductComp from './components/Product.vue';
import Calculator from './components/Calculator.vue';
import Checkout from './components/Checkout.vue';
import Success from './components/Success.vue';
import TransactionLog from './components/TransactionLog.vue';
import CreateProduct from './components/CreateProduct.vue';
import { collection, deleteDoc, doc, getDocs, orderBy, query, where } from 'firebase/firestore/lite';
import { db } from './firebase';
import DeleteProduct from './components/DeleteProduct.vue';

export interface Product {
	image: string,
	name: string,
	price: number, // cents
	quantity: number,
}

// const CalculatorComp = ref<{
// 	subtotal: number,
// 	finalTotal: number,
// } | null>(null);

const productData = ref<Product[]>([]);
const discount = ref(0); // 0.25, 0.50 etc.
const isCheckoutOpen = ref(false);
const isSuccessOpen = ref(false);
const isTransactionlogOpen = ref(false);
const isCreateProductOpen = ref(false);
const isDeleteProductOpen = ref(false);
const isTransactionSuccess = ref(false);
const chosenProductName = ref<string | null>(null);

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
	discount.value = discount.value === val ? 0 : val;
}

const toggleCheckout = () => {
	isCheckoutOpen.value = !isCheckoutOpen.value;
}

const openSuccess = (status: boolean) => {
	isSuccessOpen.value = true;
	isTransactionSuccess.value = status;
}
const closeSuccess = () => {
	isSuccessOpen.value = false;
}

const toggleTransactionLog = () => {
	isTransactionlogOpen.value = !isTransactionlogOpen.value;
}

const toggleCreateProduct = () => {
	isCreateProductOpen.value = !isCreateProductOpen.value;
}

const toggleDeleteProduct = () => {
	isDeleteProductOpen.value = !isDeleteProductOpen.value;
}

const handleChosenProductName = (value: string) => {
	chosenProductName.value = value;
}

const resetQuantity = () => {
	productData.value.forEach((item) => {
		item.quantity = 0;
	});
}

const fetchProducts = async () => {
    try {
        const productCollection = collection(db, 'products');

        const q = query(productCollection, orderBy('name', 'asc'));

        const querySnapshot = await getDocs(q);

        const fetchedData = querySnapshot.docs.map(doc => {
            const data = doc.data();
            return {
                id: doc.id,
                image: data.image,
                name: data.name,
                price: (data.priceCents || 0), 
                quantity: 0,
            };
        });

        productData.value = fetchedData;
    } catch (error) {
        console.error("Error fetching products:", error);
    }
};

onMounted(() => {
	fetchProducts();
});
</script>

<template>
	<div class="relative w-screen h-screen flex">
		<div class="w-3/5">
			<ProductComp 
				:productData="productData" 
				:addQuantity="addQuantity"
				@toggle-transaction-log="toggleTransactionLog"
				@toggle-create-product="toggleCreateProduct"
				:fetchProducts="fetchProducts"
				@chosenProductName="handleChosenProductName"
				@toggle-delete-product="toggleDeleteProduct"
			/>
		</div>
		<div class="w-2/5 border-red-700 border-l h-full">
			<Calculator 
				ref="CalculatorComp" 
				:productData="productData" 
				:discount="discount" 
				:addQuantity="addQuantity"
				:subtractQuantity="subtractQuantity" 
				:setDiscount="setDiscount" 
				@open-checkout="toggleCheckout" 
			/>
		</div>
	</div>

	<Checkout 
		v-if="isCheckoutOpen" 
		:productData="productData" 
		:discount="discount" 
		@close="toggleCheckout" 
		@open-success="(status) => openSuccess(status)"
		@close-success="closeSuccess"
	/>

	<Success 
		v-if="isSuccessOpen"
		@close="toggleCheckout" 
		@close-success="closeSuccess"
		:isTransactionSuccess="isTransactionSuccess"
		:resetQuantity="resetQuantity"
	/>

	<TransactionLog 
		v-if="isTransactionlogOpen"
		@toggle-transaction-log="toggleTransactionLog"
	/>

	<CreateProduct 
		v-if="isCreateProductOpen"
		@toggle-create-product="toggleCreateProduct"
		:fetchProducts="fetchProducts"
	/>

	<DeleteProduct 
		v-if="isDeleteProductOpen"
		@toggle-delete-product="toggleDeleteProduct"
		:productName="chosenProductName"
		:fetchProducts="fetchProducts"
	/>
</template>