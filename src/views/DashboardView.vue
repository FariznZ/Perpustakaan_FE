<script setup>
import { computed, ref, onMounted } from 'vue'
import { useAuthStore } from '../stores/authStore'
import { useBookStore } from '../stores/bookStore'
import { useLoanStore } from '../stores/loanStore'
import Icon from '../components/Icon.vue'
import BookCard from '../components/BookCard.vue'

const auth = useAuthStore()
const bookStore = useBookStore()
const loanStore = useLoanStore()

const totalUsersCount = ref(0)

onMounted(async () => {
  if (auth.user?.role === 'admin') {
    const allUsers = await auth.fetchAllUsers()
    if (allUsers) totalUsersCount.value = allUsers.length
  }
})

const stats = computed(() => {
  if (auth.user?.role === 'admin') {
    return [
      { label: 'Total Buku', value: bookStore.books.length, icon: 'book', color: 'text-indigo-600', bg: 'bg-indigo-50' },
      { label: 'Peminjaman Aktif', value: loanStore.loans.filter(l => l.status === 'dipinjam' || l.status === 'menunggu').length, icon: 'bookmark', color: 'text-amber-600', bg: 'bg-amber-50' },
      { label: 'Total Member', value: totalUsersCount.value, icon: 'users', color: 'text-emerald-600', bg: 'bg-emerald-50' }
    ]
  } else if (auth.user) {
    const userL = loanStore.userLoans(auth.user.id)
    return [
      { label: 'Buku Dipinjam', value: userL.filter(l => l.status === 'dipinjam').length, icon: 'book', color: 'text-indigo-600', bg: 'bg-indigo-50' },
      { label: 'Menunggu', value: userL.filter(l => l.status === 'menunggu').length, icon: 'bookmark', color: 'text-amber-600', bg: 'bg-amber-50' },
      { label: 'Dikembalikan', value: userL.filter(l => l.status === 'dikembalikan').length, icon: 'bookmark', color: 'text-emerald-600', bg: 'bg-emerald-50' }
    ]
  }
  return []
})

const recentBooks = computed(() => [...bookStore.books].reverse().slice(0, 4))
</script>

<template>
  <div class="space-y-8">
    <!-- Hero -->
    <div class="bg-gradient-to-br from-indigo-600 via-indigo-500 to-purple-600 rounded-3xl p-8 md:p-10 text-white relative overflow-hidden">
      <div class="absolute top-0 right-0 w-64 h-64 bg-white/5 rounded-full -translate-y-1/2 translate-x-1/4"></div>
      <div class="absolute bottom-0 left-0 w-48 h-48 bg-white/5 rounded-full translate-y-1/2 -translate-x-1/4"></div>
      <div class="relative z-10">
        <h1 class="text-2xl md:text-4xl font-bold mb-3 tracking-tight">Selamat Datang, {{ auth.user?.username || auth.user?.name }}!</h1>
        <p class="text-indigo-100 mb-6 max-w-xl">Jelajahi koleksi buku digital dan fisik dari perpustakaan kami. Temukan bacaan favoritmu di sini.</p>
        <router-link to="/catalog" class="inline-flex items-center gap-2 bg-white text-indigo-600 font-semibold px-5 py-2.5 rounded-xl hover:bg-indigo-50 transition shadow-lg shadow-indigo-900/20">
          <Icon name="search" size="18" /> Jelajahi Katalog
        </router-link>
      </div>
    </div>

    <!-- Upgrade Banner -->
    <div v-if="auth.user?.role === 'user' && auth.user?.membership !== 'premium'" class="bg-gradient-to-r from-amber-400 to-orange-400 rounded-2xl p-6 text-white flex flex-col md:flex-row items-center justify-between shadow-md">
      <div class="flex items-start gap-3">
        <div class="p-2 bg-white/20 rounded-xl shrink-0">
          <Icon name="crown" size="22" />
        </div>
        <div>
          <h3 class="text-lg font-bold">Upgrade ke Premium</h3>
          <p class="text-amber-50 text-sm">Baca penuh, simpan progres, dan unduh PDF.</p>
        </div>
      </div>
      <router-link to="/upgrade" class="mt-3 md:mt-0 bg-white text-amber-600 font-semibold px-5 py-2 rounded-xl hover:bg-amber-50 transition shrink-0 text-sm shadow-sm">Lihat Paket</router-link>
    </div>

    <!-- Mode Info -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-5">
      <div class="bg-white p-5 rounded-2xl border border-slate-200 flex gap-4 items-start card-hover">
        <div class="p-3 bg-indigo-50 text-indigo-500 rounded-xl shrink-0"><Icon name="monitor" size="22"/></div>
        <div>
          <h3 class="font-bold text-slate-800">E-Book Digital</h3>
          <p class="text-slate-500 text-sm mt-0.5">Baca langsung di browser dengan PDF Reader interaktif, bookmark, dan navigasi halaman.</p>
        </div>
      </div>
      <div class="bg-white p-5 rounded-2xl border border-slate-200 flex gap-4 items-start card-hover">
        <div class="p-3 bg-amber-50 text-amber-500 rounded-xl shrink-0"><Icon name="book" size="22"/></div>
        <div>
          <h3 class="font-bold text-slate-800">Buku Fisik</h3>
          <p class="text-slate-500 text-sm mt-0.5">Ajukan peminjaman, ambil di perpustakaan setelah disetujui, pantau riwayat dengan mudah.</p>
        </div>
      </div>
    </div>

    <!-- Stats -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-5">
      <div v-for="stat in stats" :key="stat.label" class="bg-white p-5 rounded-2xl border border-slate-200 flex items-center gap-4 card-hover">
        <div :class="[stat.bg, stat.color]" class="p-3.5 rounded-xl shrink-0">
          <Icon :name="stat.icon" size="22" />
        </div>
        <div>
          <div class="text-2xl font-bold text-slate-800">{{ stat.value }}</div>
          <div class="text-sm text-slate-500 font-medium">{{ stat.label }}</div>
        </div>
      </div>
    </div>

    <!-- Recent Books -->
    <div>
      <div class="flex justify-between items-center mb-5">
        <h2 class="text-lg font-bold text-slate-800">Buku Terbaru</h2>
        <router-link to="/catalog" class="text-sm text-indigo-600 hover:text-indigo-700 font-medium">Lihat semua →</router-link>
      </div>
      <div v-if="recentBooks.length > 0" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-5">
        <BookCard v-for="book in recentBooks" :key="book.id" :book="book" />
      </div>
      <div v-else class="text-center py-12 bg-white border border-slate-200 rounded-2xl">
        <Icon name="book" size="32" class="text-slate-300 mx-auto mb-3" />
        <p class="text-slate-500 text-sm">Belum ada buku di database.</p>
      </div>
    </div>
  </div>
</template>
