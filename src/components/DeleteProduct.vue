<script setup lang="ts">
import { ref } from 'vue';
import { db } from '../firebase'; 
import { collection, query, where, getDocs, doc, deleteDoc } from 'firebase/firestore/lite';

const emit = defineEmits<{
    (e: 'toggle-delete-product'): void
}>();

const props = defineProps<{
    fetchProducts: Function,
    productName: string | null,
}>();

const isSubmitting = ref(false);

const handleDelete = async () => {
    if (isSubmitting.value) return;
    
    isSubmitting.value = true;
    try {
        const productCol = collection(db, 'products');
        const q = query(productCol, where('name', '==', props.productName));
        const querySnapshot = await getDocs(q);

        const deletePromises = querySnapshot.docs.map((document) => {
            return deleteDoc(doc(db, 'products', document.id));
        });

        await Promise.all(deletePromises);
        
        await props.fetchProducts();
        emit('toggle-delete-product');
    } catch (error) {
        console.error("Delete failed:", error);
        alert("Could not delete product. Check console.");
    } finally {
        isSubmitting.value = false;
    }
};

</script>

<template>
    <div @click.self="emit('toggle-delete-product')"
        class="fixed top-0 left-0 z-50 w-screen h-screen bg-black/60 backdrop-blur-sm flex flex-col justify-center items-center py-5 gap-7">

        <div @click="emit('toggle-delete-product')"
            class="absolute z-[51] top-5 right-5 hover:bg-white/20 w-[50px] h-[50px] cursor-pointer rounded-full flex items-center justify-center transition">
            <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24">
                <path fill="#fff"
                    d="M10.586 12L2.793 4.207l1.414-1.414L12 10.586l7.793-7.793l1.414 1.414L13.414 12l7.793 7.793l-1.414 1.414L12 13.414l-7.793 7.793l-1.414-1.414z" />
            </svg>
        </div>

        <div class="w-[400px] h-fit bg-white flex flex-col rounded-[20px] shadow-2xl overflow-clip text-center">
            <div class="flex-none bg-red-600 py-4 px-6 text-white text-left">
                <h1 class="text-xl font-bold">Delete Product?</h1>
            </div>

            <div class="p-8 flex flex-col gap-6">
                <div>
                    <p class="text-gray-500 text-sm uppercase font-bold tracking-widest">Warning</p>
                    <p class="text-lg text-gray-800">
                        You are about to delete <br/>
                        <span class="font-bold text-red-600">"{{ productName }}"</span>
                    </p>
                    <p class="text-gray-400 text-xs mt-2 italic">This action cannot be undone.</p>
                </div>

                <div class="flex flex-col gap-3">
                    <button 
                        @click="handleDelete"
                        :disabled="isSubmitting"
                        class="bg-red-600 hover:bg-red-700 text-white font-bold py-4 rounded-xl transition active:scale-95 disabled:opacity-50">
                        {{ isSubmitting ? 'Deleting...' : 'Yes, Delete Product' }}
                    </button>
                    
                    <button 
                        @click="emit('toggle-delete-product')"
                        class="bg-gray-100 hover:bg-gray-200 text-gray-600 font-bold py-3 rounded-xl transition">
                        Cancel
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>