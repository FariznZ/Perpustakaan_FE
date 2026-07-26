<script setup>
import { useRouter } from 'vue-router'
import Icon from './Icon.vue'

defineProps({ book: Object })

const router = useRouter()
</script>

<template>
  <div class="bg-white rounded-2xl border border-slate-200 overflow-hidden hover:shadow-lg hover:-translate-y-0.5 transition-all duration-200 flex flex-col h-full cursor-pointer group" @click="router.push('/book/' + book.id)">
    <div class="h-48 overflow-hidden relative">
      <img :src="book.foto_buku || book.cover" alt="Cover" class="w-full h-full object-cover bg-slate-200 group-hover:scale-105 transition-transform duration-300">
      <div class="absolute top-3 right-3 px-2.5 py-1 rounded-lg text-[11px] font-bold shadow-md backdrop-blur-sm"
        :class="(book.pdf_buku && book.pdf_buku !== 'null') || book.type === 'digital' ? 'bg-indigo-500/90 text-white' : 'bg-amber-500/90 text-white'">
        {{ (book.pdf_buku && book.pdf_buku !== 'null') || book.type === 'digital' ? 'E-Book' : 'Fisik' }}
      </div>
    </div>
    <div class="p-4 flex flex-col flex-grow">
      <div class="flex items-center gap-1 text-amber-400 mb-1.5">
        <Icon name="star" size="13" />
        <span class="text-xs font-bold">{{ book.rating || '0.0' }}</span>
        <span class="text-xs text-slate-400">({{ book.reviews?.length || 0 }})</span>
      </div>
      <h3 class="font-bold text-slate-800 leading-snug mb-1 line-clamp-2 text-sm">{{ book.nama_buku || book.title }}</h3>
      <p class="text-xs text-slate-500 mb-3">{{ book.penulis || book.author || 'Penulis Tidak Diketahui' }}</p>
      <div class="mt-auto pt-3 border-t border-slate-100 flex justify-between items-center">
        <span class="text-slate-500 font-medium text-[11px] uppercase tracking-wider">{{ book.jenis_buku || book.category }}</span>
        <span v-if="!(book.pdf_buku && book.pdf_buku !== 'null') && book.type !== 'digital'"
          :class="(book.stok !== undefined ? book.stok : book.stock) > 0 ? 'text-emerald-600' : 'text-red-500'"
          class="font-semibold text-xs">
          {{ (book.stok !== undefined ? book.stok : book.stock) > 0 ? 'Stok: ' + (book.stok !== undefined ? book.stok : book.stock) : 'Habis' }}
        </span>
        <span v-else class="text-indigo-600 font-semibold text-xs">Digital</span>
      </div>
    </div>
  </div>
</template>