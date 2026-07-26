<script setup>
import { ref, computed } from 'vue'
import { useBookStore } from '../stores/bookStore'
import BookCard from '../components/BookCard.vue'
import Icon from '../components/Icon.vue'

const bookStore = useBookStore()

const searchQuery = ref('')
const filterMode = ref('all')
const filterCategory = ref('all')

const categories = computed(() => ['all', ...new Set(bookStore.books.map(b => b.jenis_buku || b.category))])

const filteredBooks = computed(() => {
  return bookStore.books.filter(b => {
      const title = b.nama_buku || b.title || ''
      const author = b.penulis || b.author || ''
      const matchSearch = title.toLowerCase().includes(searchQuery.value.toLowerCase()) || 
                          author.toLowerCase().includes(searchQuery.value.toLowerCase())
      const hasPdf = b.pdf_buku && b.pdf_buku !== '' && b.pdf_buku !== 'null'
      const virtualBookType = hasPdf ? 'digital' : 'physical'
      const matchMode = filterMode.value === 'all' || virtualBookType === filterMode.value
      const currentCategory = b.jenis_buku || b.category
      const matchCat = filterCategory.value === 'all' || currentCategory === filterCategory.value
      return matchSearch && matchMode && matchCat
  })
})
</script>

<template>
  <div>
    <div class="flex items-center justify-between mb-6">
      <div>
        <h1 class="text-2xl font-bold text-slate-900 tracking-tight">Katalog Buku</h1>
        <p class="text-slate-500 text-sm mt-1">Temukan bacaan favoritmu</p>
      </div>
      <span class="text-sm text-slate-400 font-medium bg-white px-3 py-1.5 rounded-xl border border-slate-200">{{ filteredBooks.length }} buku</span>
    </div>
    
    <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200 mb-8 flex flex-col md:flex-row gap-3 md:items-center">
      <div class="relative flex-1 min-w-0 w-full">
        <div class="absolute inset-y-0 left-0 pl-3.5 flex items-center pointer-events-none text-slate-400">
          <Icon name="search" size="17"/>
        </div>
        <input v-model="searchQuery" type="text" placeholder="Cari judul atau penulis..." class="w-full pl-10 pr-4 py-2.5 rounded-xl border border-slate-200 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 outline-none text-sm bg-white/50">
      </div>
      <div class="flex gap-3 w-full md:w-auto md:flex-shrink-0">
        <select v-model="filterMode" class="flex-1 md:flex-none md:w-40 py-2.5 px-3.5 rounded-xl border border-slate-200 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 outline-none bg-white text-sm cursor-pointer">
          <option value="all">Semua Mode</option>
          <option value="digital">E-Book</option>
          <option value="physical">Fisik</option>
        </select>
        <select v-model="filterCategory" class="flex-1 md:flex-none md:w-48 py-2.5 px-3.5 rounded-xl border border-slate-200 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 outline-none bg-white text-sm cursor-pointer">
          <option v-for="cat in categories" :key="cat" :value="cat">{{ cat === 'all' ? 'Semua Kategori' : cat }}</option>
        </select>
      </div>
    </div>

    <div v-if="filteredBooks.length > 0" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-5">
      <BookCard v-for="book in filteredBooks" :key="book.id" :book="book" />
    </div>
    <div v-else class="text-center py-16">
      <div class="w-16 h-16 bg-slate-100 rounded-2xl flex items-center justify-center mx-auto mb-4 text-slate-400">
         <Icon name="search" size="24" />
      </div>
      <p class="text-slate-500 font-medium">Buku tidak ditemukan</p>
      <p class="text-slate-400 text-sm mt-1">Coba ubah kata kunci atau filter pencarian</p>
    </div>
  </div>
</template>