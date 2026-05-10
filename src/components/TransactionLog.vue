<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import { db } from '../firebase'; // Ensure path is correct
import { collection, getDocs, query, orderBy } from 'firebase/firestore/lite';

const emit = defineEmits<{
    (e: 'toggle-transaction-log'): void
}>();

const orders = ref<any[]>([]);
const loading = ref(true);

const fetchOrders = async () => {
    loading.value = true;
    try {
        const ordersCol = collection(db, 'orders');

        const q = query(ordersCol, orderBy('timestamp', 'desc'));

        const querySnapshot = await getDocs(q);

        orders.value = querySnapshot.docs.map(doc => ({
            id: doc.id,
            ...doc.data()
        }));
    } catch (error) {
        console.error("Error fetching orders:", error);
    } finally {
        loading.value = false;
    }
};

const totalSales = computed(() => {
    const sumCents = orders.value.reduce((balance, order) => {
        if (order.isSuccess) {
            return balance + (order.totalCents || 0);
        }
        return balance;
    }, 0);

    return sumCents;
});

onMounted(fetchOrders);
</script>

<template>
    <div @click.self="emit('toggle-transaction-log')"
        class="fixed top-0 left-0 z-50 w-screen h-screen bg-black/60 backdrop-blur-sm flex flex-col justify-center items-center gap-7">

        <div @click="emit('toggle-transaction-log')"
            class="absolute z-[51] top-5 right-5 hover:bg-white/20 w-[50px] h-[50px] cursor-pointer rounded-full flex items-center justify-center transition">
            <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24">
                <path fill="#fff"
                    d="M10.586 12L2.793 4.207l1.414-1.414L12 10.586l7.793-7.793l1.414 1.414L13.414 12l7.793 7.793l-1.414 1.414L12 13.414l-7.793 7.793l-1.414-1.414z" />
            </svg>
        </div>

        <div class="w-[400px] h-[500px] bg-white flex flex-col rounded-[20px] shadow-2xl overflow-clip">
            <div class="flex-none bg-red-600 py-4 px-6 text-white">
                <h1 class="text-xl font-bold">Transaction Log</h1>
            </div>

            <div class="grow overflow-y-auto p-4 flex flex-col gap-3">
                <div v-if="loading" class="text-center py-10 text-gray-400 italic">
                    Loading Pitapan orders...
                </div>

                <div v-else-if="orders.length === 0" class="text-center py-10 text-gray-400">
                    No transactions found.
                </div>

                <div v-for="order in orders" :key="order.id"
                    class="border border-gray-100 p-4 rounded-xl shadow-sm hover:border-red-200 transition">
                    <div class="flex justify-between items-start">
                        <div>
                            <p class="font-bold text-gray-800">Total: RM {{ (parseInt(order.totalCents) / 100)?.toFixed(2) }}</p>
                            <p class="text-xs text-gray-400 uppercase tracking-tight">ID: {{ order.id.slice(0, 8) }}</p>
                        </div>
                        <div class="w-fit flex flex-col gap-2 justify-between items-end">
                            <span class="text-[10px] bg-gray-100 px-2 py-1 rounded text-gray-500">
                                {{ order.timestamp?.toDate().toLocaleTimeString() }}
                            </span>
                            <div class="flex gap-2 items-center">
                                <p v-if="order.isSuccess" class="text-xs text-green-600 tracking-tight">success</p>
                                <p v-else class="text-xs text-red-600 tracking-tight">failed</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="w-[400px] bg-white flex rounded-[20px] shadow-2xl overflow-clip">
            <div class="w-1/4 bg-red-600 py-4 px-6 text-white text-xl font-bold">Sales</div>
            <div class="w-3/4 text-3xl font-black text-right flex items-end justify-end px-7 py-3 gap-1"><span class="text-base">RM</span>{{ (totalSales / 100).toFixed(2) }}</div>
        </div>

    </div>
</template>