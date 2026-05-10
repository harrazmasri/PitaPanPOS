<script setup lang="ts">
import { ref } from 'vue';
import { db } from '../firebase'; 
import { collection, addDoc, serverTimestamp } from 'firebase/firestore/lite';

const emit = defineEmits<{
    (e: 'toggle-create-product'): void
}>();

const name = ref('');
const priceCents = ref<number | null>(null);
const imageUrl = ref('');
const isSubmitting = ref(false);
const props = defineProps<{
    fetchProducts: Function,
}>();

const handleAddProduct = async () => {
    if (!name.value || !priceCents.value || !imageUrl.value) {
        alert("Please fill in all fields!");
        return;
    }

    isSubmitting.value = true;
    try {
        await addDoc(collection(db, 'products'), {
            name: name.value,
            priceCents: Number(priceCents.value),
            image: imageUrl.value,
            createdAt: serverTimestamp()
        });

        emit('toggle-create-product');
        props.fetchProducts();
    } catch (error) {
        console.error("Error adding product:", error);
        alert("Failed to save product.");
    } finally {
        isSubmitting.value = false;
    }
};
</script>

<template>
    <div @click.self="emit('toggle-create-product')"
        class="fixed top-0 left-0 z-50 w-screen h-screen bg-black/60 backdrop-blur-sm flex flex-col justify-center items-center gap-7">

        <div @click="emit('toggle-create-product')"
            class="absolute z-[51] top-5 right-5 hover:bg-white/20 w-[50px] h-[50px] cursor-pointer rounded-full flex items-center justify-center transition">
            <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24">
                <path fill="#fff"
                    d="M10.586 12L2.793 4.207l1.414-1.414L12 10.586l7.793-7.793l1.414 1.414L13.414 12l7.793 7.793l-1.414 1.414L12 13.414l-7.793 7.793l-1.414-1.414z" />
            </svg>
        </div>

        <div class="w-[400px] h-fit bg-white flex flex-col rounded-[20px] shadow-2xl overflow-clip">
            <div class="flex-none bg-red-600 py-4 px-6 text-white">
                <h1 class="text-xl font-bold">Add New Product</h1>
            </div>

            <form @submit.prevent="handleAddProduct" class="p-6 flex flex-col gap-4">
                <div class="flex flex-col gap-1">
                    <label class="text-xs font-bold text-gray-500 uppercase">Product Name</label>
                    <input v-model="name" type="text" placeholder="e.g. Lamb Kebab" 
                        class="border-2 border-gray-100 rounded-lg p-2 focus:border-red-500 outline-none transition" required />
                </div>

                <div class="flex flex-col gap-1">
                    <label class="text-xs font-bold text-gray-500 uppercase">Price (in Cents)</label>
                    <input v-model="priceCents" type="number" placeholder="e.g. 1500 for RM 15.00" 
                        class="border-2 border-gray-100 rounded-lg p-2 focus:border-red-500 outline-none transition" required />
                </div>

                <div class="flex flex-col gap-1">
                    <label class="text-xs font-bold text-gray-500 uppercase">Image URL</label>
                    <input v-model="imageUrl" type="text" placeholder="https://image-link.com/photo.jpg" 
                        class="border-2 border-gray-100 rounded-lg p-2 focus:border-red-500 outline-none transition" required />
                </div>

                <div v-if="imageUrl" class="w-full h-32 bg-gray-100 rounded-lg overflow-hidden flex items-center justify-center">
                    <img :src="imageUrl" alt="Preview" class="h-full w-full object-cover" />
                </div>

                <button type="submit" :disabled="isSubmitting"
                    class="bg-red-600 text-white font-bold py-3 rounded-xl mt-2 hover:bg-red-700 active:scale-95 transition disabled:opacity-50">
                    {{ isSubmitting ? 'Saving...' : 'Save Product' }}
                </button>
            </form>
        </div>
    </div>
</template>