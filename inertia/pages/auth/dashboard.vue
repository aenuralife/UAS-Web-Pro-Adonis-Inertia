<script setup lang="ts">
import { ref, computed } from 'vue'
import { router } from '@inertiajs/vue3'

interface MenuItem {
  id: number;
  name: string;
  price: number;
  image: string;
  category: string; // Pastikan ini ada dan string
}

const menuItems = ref<MenuItem[]>([
  { id: 1, name: 'Men Striped Print Shirt Without Tee', price: 200000, image: 'https://i.pinimg.com/736x/0a/71/e1/0a71e197488f4690afae3424e917152c.jpg', category: 'Pria' },
  { id: 2, name: 'Modest Long Sleeve Maxi Dress', price: 300000, image: 'https://i.pinimg.com/736x/59/7d/b3/597db3d1d7aa502926e0e4b6ecb40476.jpg', category: 'Wanita' },
  { id: 3, name: 'Color Block Short Sleeve Shirt', price: 210000, image: 'https://i.pinimg.com/736x/08/db/63/08db63594faefdfd4462ed31184a90ef.jpg', category: 'Anak'},
  { id: 4, name: 'Baby Henley Top and Shorts Set', price: 250000, image: 'https://i.pinimg.com/736x/b1/3d/43/b13d43f60eb577d329b39420f662506e.jpg', category: 'Bayi' },
  { id: 5, name: 'Men Relaxed Fit', price: 250000, image: 'https://i.pinimg.com/736x/97/9d/f1/979df1dc90f7b4df9f2c83b4fe7139c4.jpg', category: 'Pria' },
  { id: 6, name: 'Long Sleeve Tunic', price: 450000, image: 'https://i.pinimg.com/736x/6c/19/73/6c1973c089e592bc3ccf4a6f59b3f154.jpg', category: 'Wanita' },
  { id: 7, name: 'Victorian Style Baby Dress', price: 180000, image: 'https://i.pinimg.com/736x/40/7a/c7/407ac732c9bf732760c4fae49599e7c1.jpg', category: 'Anak' },
  { id: 8, name: 'Kids Casual Two-Piece', price: 350000, image: 'https://i.pinimg.com/736x/eb/5b/c0/eb5bc0d68b4d2e644f28cf8e972f91bf.jpg', category: 'Bayi' },
  // Tambahan produk dengan kategori yang jelas untuk demo filter
  { id: 9, name: 'Men Short-Sleeved T-shirt', price: 505000, image: 'https://i.pinimg.com/736x/e4/b8/7e/e4b87ef5bc5041bd7b8a30236fabac3b.jpg', category: 'Pria' },
  { id: 10, name: 'Floral OPen Front Shawl', price: 320000, image: 'https://i.pinimg.com/736x/41/01/db/4101dbd071a6d900f8cbc5f6c13e8d0a.jpg', category: 'Wanita' },
  { id: 11, name: 'Polo Shirt and Shorts Set', price: 280000, image: 'https://i.pinimg.com/736x/46/d6/7b/46d67bbf93072685a0075de4b5732e95.jpg', category: 'Anak' },
  { id: 12, name: 'Baby Girl Party Dress', price: 280000, image: 'https://i.pinimg.com/736x/07/35/83/073583d8a695e6fa5e896fbb97b4178f.jpg', category: 'Bayi' },
  { id: 13, name: 'Asymmetric Batik Shirt', price: 400000, image: 'https://i.pinimg.com/736x/01/c2/68/01c2681f9ac26889059842a85813970e.jpg', category: 'Pria' },
  { id: 14, name: 'Striped Tunic Top', price: 600000, image: 'https://i.pinimg.com/736x/f3/e7/95/f3e795708435401e8bab0b010c44d487.jpg', category: 'Wanita' },
  { id: 15, name: 'Toddler Jumper Dress', price: 150000, image: 'https://i.pinimg.com/736x/e8/23/c5/e823c5969a6da562600597606984bc6e.jpg', category: 'Anak' },
  { id: 16, name: 'Baby Sleepsuit', price: 120000, image: 'https://i.pinimg.com/736x/c8/5c/52/c85c5286b57c609e671f59a098b939d5.jpg', category: 'Bayi' },

])

// State untuk kategori yang aktif, default ke 'Semua' agar semua produk tampil awalnya
const activeCategory = ref('Semua')

// Fungsi untuk mengubah kategori aktif
function setActiveCategory(category: string) {
  activeCategory.value = category
}
function goToCheckout() {
  router.visit('/checkout')
}
function buyNow(product: MenuItem) {
  localStorage.setItem('checkoutItem', JSON.stringify({ ...product, quantity: 1 }))
  router.visit('/checkout')     
}

// Computed property untuk memfilter menuItems berdasarkan kategori aktif
const filteredMenuItems = computed(() => {
  if (activeCategory.value === 'Semua') {
    return menuItems.value
  } else {
    return menuItems.value.filter(item => item.category === activeCategory.value)
  }
})

// Ambil jumlah item di keranjang dari localStorage
const totalCartItems = computed(() => {
  const cart = JSON.parse(localStorage.getItem('cart') || '[]')
  return cart.reduce((sum: number, item: any) => sum + (item.quantity || 1), 0)
})

const addToCart = (item: MenuItem) => {
  const cart = JSON.parse(localStorage.getItem('cart') || '[]')
  const existing = cart.find((i: any) => i.id === item.id)
  if (existing) {
    existing.quantity += 1
  } else {
    cart.push({ ...item, quantity: 1 })
  }
  localStorage.setItem('cart', JSON.stringify(cart))
  window.dispatchEvent(new Event('storage')) // Update badge di header
  router.visit('/cart')
}
</script>

<template>
  <div class="min-h-screen bg-gray-100">
    <header class="bg-white shadow-md py-6 px-8 flex flex-col items-center justify-center text-center">
      <div class="flex justify-center space-x-2 mb-8 bg-black rounded-full px-4 py-2">
        <button @click="setActiveCategory('Wanita')" :class="{'bg-yellow-500': activeCategory === 'Wanita', 'bg-black': activeCategory !== 'Wanita'}" class="px-4 py-2 text-white rounded-full">WANITA</button>
        <button @click="setActiveCategory('Pria')" :class="{'bg-yellow-500': activeCategory === 'Pria', 'bg-black': activeCategory !== 'Pria'}" class="px-4 py-2 text-white rounded-full">PRIA</button>
        <button @click="setActiveCategory('Anak')" :class="{'bg-yellow-500': activeCategory === 'Anak', 'bg-black': activeCategory !== 'Anak'}" class="px-4 py-2 text-white rounded-full">ANAK</button>
        <button @click="setActiveCategory('Bayi')" :class="{'bg-yellow-500': activeCategory === 'Bayi', 'bg-black': activeCategory !== 'Bayi'}" class="px-4 py-2 text-white rounded-full">BAYI</button>
        <button @click="setActiveCategory('Semua')" :class="{'bg-yellow-500': activeCategory === 'Semua', 'bg-black': activeCategory !== 'Semua'}" class="px-4 py-2 text-white rounded-full">SEMUA</button>
      </div>
    </header>

    <main class="p-8">
      <div class="bg-white shadow-lg rounded-lg p-6 mb-8">
        <h1 class="text-3xl font-bold mb-6 text-gray-800 text-center">
          Koleksi {{ activeCategory === 'Semua' ? 'Semua' : activeCategory }} Produk
        </h1>

        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
          <div
            v-for="product in filteredMenuItems"
            :key="product.id"
            class="border rounded-lg p-4 flex flex-col"
          >
          <div class="border rounded-lg p-4 flex flex-col">
            <div class="w-full aspect-[3/4] mb-4 overflow-hidden rounded-lg bg-gray-100">
              <img
                :src="product.image"
                :alt="product.name"
                class="w-full h-full object-cover"
              />
            </div>
            <h2 class=" text-black text-lg font-semibold">{{ product.name }}</h2>
            <p class="text-black">Rp {{ product.price.toLocaleString() }}</p>
            </div>
            <button
              @click="addToCart(product)"
              class="mt-4 bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-600"
            >
              Tambah ke Keranjang
            </button>
            <button
              @click="buyNow(product)"
              class="mt-4 bg-green-500 text-white py-2 px-4 rounded-lg hover:bg-green-600 text-lg transition"
            >
               Beli
            </button>
          </div>
        </div>
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

button {
  transition: background-color 0.3s;
}

button:hover {
  cursor: pointer;
}

footer {
  font-size: 0.9rem;
}

.relative {
  position: relative;
}
</style>
