<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { Link } from '@inertiajs/vue3'

const name = ref('')
const email = ref('')
const phone = ref('')
const address = ref('')
const paymentMethod = ref('transfer')
const errors = ref<{ [key: string]: string }>({})

// Ambil data keranjang dari localStorage
const cartItems = ref<any[]>([])
const total = computed(() =>
  cartItems.value.reduce((sum, item) => sum + item.price * item.quantity, 0)
)

onMounted(() => {
  const checkoutItem = localStorage.getItem('checkoutItem')
  if (checkoutItem) {
    cartItems.value = [JSON.parse(checkoutItem)]
    // Hapus setelah diambil agar tidak tertinggal untuk transaksi berikutnya
    localStorage.removeItem('checkoutItem')
  } else {
    cartItems.value = JSON.parse(localStorage.getItem('cart') || '[]')
  }
})

function validate() {
  errors.value = {}
  if (!name.value) errors.value.name = 'Nama wajib diisi'
  if (!email.value) errors.value.email = 'Email wajib diisi'
  if (!phone.value) errors.value.phone = 'No. HP wajib diisi'
  if (!address.value) errors.value.address = 'Alamat wajib diisi'
  return Object.keys(errors.value).length === 0
}

function payNow() {
  if (!validate()) return
  // Simulasi proses pembayaran
  alert('Pembayaran berhasil!\nTerima kasih sudah berbelanja.')
  localStorage.removeItem('cart')
  window.location.href = '/'
}
</script>

<template>
  <div class="text-black min-h-screen bg-gray-100 flex flex-col items-center py-10">
    <div class="w-full max-w-4xl bg-white rounded-lg shadow-lg p-8 flex flex-col md:flex-row gap-8">
      <!-- Form Data Pembeli -->
      <div class="flex-1">
        <h2 class="text-2xl font-bold mb-6">Data Pembeli</h2>
        <form @submit.prevent="payNow" class="space-y-4">
          <div>
            <label class="block font-semibold mb-1">Nama Lengkap</label>
            <input v-model="name" type="text" class="w-full border rounded px-3 py-2" />
            <span v-if="errors.name" class="text-red-500 text-xs">{{ errors.name }}</span>
          </div>
          <div>
            <label class="block font-semibold mb-1">Email</label>
            <input v-model="email" type="email" class="w-full border rounded px-3 py-2" />
            <span v-if="errors.email" class="text-red-500 text-xs">{{ errors.email }}</span>
          </div>
          <div>
            <label class="block font-semibold mb-1">No. HP</label>
            <input v-model="phone" type="tel" class="w-full border rounded px-3 py-2" />
            <span v-if="errors.phone" class="text-red-500 text-xs">{{ errors.phone }}</span>
          </div>
          <div>
            <label class="block font-semibold mb-1">Alamat Pengiriman</label>
            <textarea v-model="address" class="w-full border rounded px-3 py-2"></textarea>
            <span v-if="errors.address" class="text-red-500 text-xs">{{ errors.address }}</span>
          </div>
          <div class="text-black font-semibold mb-2">
            <label class="block font-semibold mb-1">Metode Pembayaran</label>
            <select v-model="paymentMethod" class="w-full border rounded px-3 py-2">
              <option value="transfer">Transfer Bank</option>
              <option value="cod">COD (Bayar di Tempat)</option>
              <option value="ewallet">E-Wallet</option>
            </select>
          </div>
          <button type="submit" class="w-full bg-green-500 text-white py-3 rounded-lg font-bold hover:bg-green-600 transition">
            Bayar Sekarang
          </button>
        </form>
      </div>
      <!-- Ringkasan Belanja -->
      <div class="flex-1">
        <h2 class="text-2xl font-bold mb-6">Ringkasan Belanja</h2>
        <div v-if="cartItems.length">
          <ul class="divide-y">
            <li v-for="item in cartItems" :key="item.id" class="flex items-center py-4">
              <img :src="item.image" alt="" class="w-16 h-16 object-cover rounded mr-4 border" />
              <div class="flex-1">
                <div class="font-semibold">{{ item.name }}</div>
                <div class="text-sm text-gray-500">x{{ item.quantity }}</div>
              </div>
              <div class="font-bold text-black ml-4">Rp {{ (item.price * item.quantity).toLocaleString() }}</div>
            </li>
          </ul>
          <div class="border-t mt-6 pt-4 flex justify-between font-bold text-lg">
            <span>Total</span>
            <span>Rp {{ total.toLocaleString() }}</span>
          </div>
        </div>
        <div v-else class="text-gray-500 text-center py-12">
          Keranjang belanja kosong.
        </div>
        <Link href="/cart" class="inline-block mt-6 text-blue-600 hover:underline">← Kembali ke Keranjang</Link>
      </div>
    </div>
  </div>
</template>
