<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import { Link } from '@inertiajs/vue3'
import { router } from '@inertiajs/vue3'

const cartItems = ref<any[]>([])

function goToCheckout() {
  router.visit('/checkout')
}

onMounted(() => {
  cartItems.value = JSON.parse(localStorage.getItem('cart') || '[]')
})

const updateQuantity = (id: number, amount: number) => {
  const item = cartItems.value.find((item) => item.id === id)
  if (item) {
    item.quantity += amount
    if (item.quantity < 1) item.quantity = 1
    localStorage.setItem('cart', JSON.stringify(cartItems.value))
    window.dispatchEvent(new Event('storage'))
  }
}

const removeItem = (id: number) => {
  cartItems.value = cartItems.value.filter((item) => item.id !== id)
  localStorage.setItem('cart', JSON.stringify(cartItems.value))
  window.dispatchEvent(new Event('storage'))
  if (cartItems.value.length === 0) {
    window.location.href = '/dashboard'
  }
}

const calculateTotal = computed(() =>
  cartItems.value.reduce((total, item) => total + item.price * item.quantity, 0)
)
</script>

<template>
  <div class="min-h-screen bg-gray-100">
    <header class="bg-white shadow-md py-4 px-8 flex items-center justify-between">
      <div class="text-center">
        <Link href="/dashboard" class="inline-block mt-6 bg-yellow-500 text-black px-6 py-2 rounded-lg font-bold hover:bg-yellow-600 transition">
          ←
        </Link>
      </div>
      <h1 class="text-2xl font-bold">Keranjang Belanja</h1>
    </header>

    <main class="p-8">
      <div v-if="cartItems.length" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        <div v-for="item in cartItems" :key="item.id" class="bg-white p-6 rounded-lg shadow-lg flex flex-col">
          <div class="w-full aspect-[3/4] mb-4 overflow-hidden rounded-lg bg-gray-100">
            <img :src="item.image" :alt="item.name" class="w-full h-full object-cover" />
          </div>
          <h2 class="text-lg font-semibold mb-2">{{ item.name }}</h2>
          <p class="text-black mb-4">Rp {{ item.price.toLocaleString() }}</p>
          <div class="text-gray-400 flex items-center justify-between mb-4">
            <button @click="updateQuantity(item.id, -1)" class="px-4 py-2 bg-gray-300 rounded hover:bg-black">-</button>
            <span class="px-4">{{ item.quantity }}</span>
            <button @click="updateQuantity(item.id, 1)" class="px-4 py-2 bg-gray-300 rounded hover:bg-black">+</button>
          </div>
          <button @click="removeItem(item.id)" class="w-full bg-red-500 text-white py-2 rounded hover:bg-red-700">Hapus</button>
        </div>
      </div>

      <div v-if="cartItems.length" class="mt-12 border-t pt-6">
        <h2 class="text-black text-2xl font-semibold mb-4">Total: Rp {{ calculateTotal.toLocaleString() }}</h2>
       <button
         @click="goToCheckout"
          class="bg-green-500 text-white py-3 px-8 rounded-lg hover:bg-green-600 font-bold text-lg transition"
          >
           Lanjutkan ke Pembayaran
          </button>
      </div>
    </main>
  </div>
</template>

<style scoped>
header {
  font-family: Arial, sans-serif;
}

h1 {
  color: #333;
}

nav a {
  transition: color 0.3s;
}

nav a:hover {
  color: #ff6600;
}

button {
  transition: background-color 0.3s;
}

button:hover {
  cursor: pointer;
}

footer {
  background-color: #f9f9f9;
  border-top: 1px solid #eaeaea;
}
</style>
