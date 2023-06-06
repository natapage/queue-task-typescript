<script setup lang="ts">
import { ref, Ref, onMounted, computed } from "vue";
import checkoutQueue from "./CheckoutQueue.vue";

const queues: Ref<number[][]> = ref([
  [2, 3, 5],
  [2, 6, 3],
  [1],
  [11, 3, 6],
  [],
]);
let newPurchasesQuantity: Ref<string> = ref("");
let isCorrectQuantity: Ref<boolean> = ref(true);

function addCustomer(): void {
  const purchasesQuantityNum = Number(newPurchasesQuantity.value);
  if (purchasesQuantityNum > 0 && Number.isInteger(purchasesQuantityNum)) {
    isCorrectQuantity.value = true;
    queues.value[shortestQueueIndex.value].push(purchasesQuantityNum);
    newPurchasesQuantity.value = "";
    return;
  }
  isCorrectQuantity.value = false;
  newPurchasesQuantity.value = "";
}

function updateQueues(): void {
  queues.value.forEach((queue) => {
    if (queue.length === 0) {
      return;
    }
    if (queue[0] === 1) {
      queue.shift();
      return;
    }
    queue[0] -= 1;
  });
}

const shortestQueueIndex = computed(() => {
  return queues.value
    .map((array) => {
      return array.reduce((acc, item) => acc + item, 0);
    })
    .reduce((acc, item, index, arr) => (item < arr[acc] ? index : acc), 0);
});

onMounted(() => setInterval(updateQueues, 2000));
</script>

<template>
  <h2 style="color: red" v-if="!isCorrectQuantity">
    please enter a positive integer
  </h2>
  <div class="input-form">
    <input
      v-model="newPurchasesQuantity"
      type="number"
      class="input"
      placeholder="Add purchases quantity.."
    />
    <button class="btn" @click="addCustomer">Create customer!</button>
  </div>

  <div class="checkout-zone">
    <checkoutQueue
      v-for="(_, index) in queues"
      :key="index + 1"
      :checkoutNumber="index + 1"
      :queue="queues[index]"
    ></checkoutQueue>
  </div>
</template>

<style scoped>
.container {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.checkout-zone {
  display: flex;
}
</style>
