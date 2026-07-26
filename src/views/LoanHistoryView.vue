<script setup>
import { ref, computed, onMounted } from 'vue'
import { useAuthStore } from '../stores/authStore'
import { useLoanStore } from '../stores/loanStore'
import { useBookStore } from '../stores/bookStore'
import Icon from '../components/Icon.vue'

const auth = useAuthStore()
const loanStore = useLoanStore()
const bookStore = useBookStore()

const activeTab = ref('loans')
const combinedReadingData = ref([])

const bookmarkKey = computed(() => `book_bookmarks_${auth.user?.id || 'guest'}`)
const historyKey = computed(() => `book_history_${auth.user?.id || 'guest'}`)

const history = computed(() => {
  if (!auth.user) return []
  return loanStore.userLoans(auth.user.id).map(loan => ({ ...loan, book: bookStore.books.find(b => String(b.id) === String(loan.buku_id)) })).reverse()
})

const loadReadingActivity = () => {
  const bookmarks = JSON.parse(localStorage.getItem(bookmarkKey.value) || '[]')
  const historyList = JSON.parse(localStorage.getItem(historyKey.value) || '[]')
  const mergedMap = new Map()
  historyList.forEach(item => mergedMap.set(item.id, { ...item, isBookmarked: false, halaman_terakhir: item.halaman_terakhir || 1, tipe_label: 'Dibaca' }))
  bookmarks.forEach(item => {
    if (mergedMap.has(item.id)) { const e = mergedMap.get(item.id); mergedMap.set(item.id, { ...e, isBookmarked: true, tipe_label: 'Bookmark' }) }
    else mergedMap.set(item.id, { ...item, isBookmarked: true, halaman_terakhir: 1, tipe_label: 'Bookmark' })
  })
  combinedReadingData.value = Array.from(mergedMap.values())
}

const removeActivity = (id) => {
  let bookmarks = JSON.parse(localStorage.getItem(bookmarkKey.value) || '[]').filter(b => b.id !== id)
  localStorage.setItem(bookmarkKey.value, JSON.stringify(bookmarks))
  let historyList = JSON.parse(localStorage.getItem(historyKey.value) || '[]').filter(b => b.id !== id)
  localStorage.setItem(historyKey.value, JSON.stringify(historyList))
  loadReadingActivity()
}

onMounted(() => { loadReadingActivity() })

const formatDate = (ds) => {
  if (!ds) return '-'
  return new Date(ds).toLocaleDateString('id-ID', { day: 'numeric', month: 'short', year: 'numeric' })
}
</script>

<template>
  <div class="bg-white rounded-2xl shadow-sm border border-slate-200 overflow-hidden">
    <div class="px-6 pt-6 border-b border-slate-200">
      <h2 class="text-xl font-bold text-slate-800 mb-4">Riwayat Aktivitas</h2>
      <div class="flex gap-6 -mb-px">
        <button @click="activeTab = 'loans'" :class="activeTab === 'loans' ? 'border-indigo-500 text-indigo-600 font-semibold' : 'border-transparent text-slate-500 hover:text-slate-700'" class="pb-3 border-b-2 transition text-sm">Peminjaman Fisik</button>
        <button @click="activeTab = 'reading'; loadReadingActivity()" :class="activeTab === 'reading' ? 'border-indigo-500 text-indigo-600 font-semibold' : 'border-transparent text-slate-500 hover:text-slate-700'" class="pb-3 border-b-2 transition text-sm">Bacaan & Bookmark</button>
      </div>
    </div>
    <div class="p-6">
      <div v-if="activeTab === 'loans'">
        <div v-if="history.length === 0" class="text-center py-10 text-slate-400">Belum ada riwayat peminjaman.</div>
        <div class="overflow-x-auto" v-else>
          <table class="w-full text-left border-collapse">
            <thead><tr class="bg-slate-50 text-slate-500 text-sm border-b border-slate-200"><th class="p-3 font-medium">Judul</th><th class="p-3 font-medium">Tgl Pinjam</th><th class="p-3 font-medium">Tenggat</th><th class="p-3 font-medium text-right">Status</th></tr></thead>
            <tbody class="divide-y divide-slate-100">
              <tr v-for="loan in history" :key="loan.id" class="hover:bg-slate-50 transition">
                <td class="p-3 font-medium text-slate-800 flex items-center gap-3">
                  <img :src="loan.book?.cover || loan.book?.cover_buku" class="w-9 h-12 object-cover rounded shadow-sm bg-slate-200">
                  <span class="text-sm">{{ loan.book?.title || loan.book?.nama_buku || loan.nama_buku || 'Buku Dihapus' }}</span>
                </td>
                <td class="p-3 text-slate-600 text-sm">{{ formatDate(loan.tanggal_pinjam) }}</td>
                <td class="p-3 text-slate-600 text-sm">{{ formatDate(loan.tanggal_kembali) }}</td>
                <td class="p-3 text-right">
                  <span v-if="loan.status === 'menunggu'" class="inline-flex px-2.5 py-1 rounded-full text-xs font-semibold bg-amber-50 text-amber-700 border border-amber-200">Menunggu</span>
                  <span v-else-if="loan.status === 'dipinjam'" class="inline-flex px-2.5 py-1 rounded-full text-xs font-semibold bg-indigo-50 text-indigo-700 border border-indigo-200">Dipinjam</span>
                  <span v-else-if="loan.status === 'dikembalikan'" class="inline-flex px-2.5 py-1 rounded-full text-xs font-semibold bg-emerald-50 text-emerald-700 border border-emerald-200">Kembali</span>
                  <span v-else-if="loan.status === 'ditolak'" class="inline-flex px-2.5 py-1 rounded-full text-xs font-semibold bg-red-50 text-red-600 border border-red-200">Ditolak</span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <div v-if="activeTab === 'reading'">
        <div v-if="combinedReadingData.length === 0" class="text-center py-10 text-slate-400">Belum ada aktivitas membaca.</div>
        <div class="space-y-3" v-else>
          <div v-for="item in combinedReadingData" :key="item.id" class="border border-slate-200 rounded-xl p-4 flex gap-4 items-center bg-white card-hover">
            <img :src="item.cover_buku || item.cover" class="w-11 h-14 object-cover rounded-lg shadow-sm bg-slate-200 shrink-0">
            <div class="flex-grow min-w-0">
              <div class="flex items-center gap-2">
                <h4 class="font-semibold text-slate-800 text-sm truncate">{{ item.nama_buku || item.title }}</h4>
                <span :class="item.isBookmarked ? 'bg-amber-50 text-amber-700 border-amber-200' : 'bg-indigo-50 text-indigo-700 border-indigo-200'" class="text-[10px] font-semibold px-2 py-0.5 rounded-full border shrink-0">{{ item.tipe_label }}</span>
              </div>
              <p class="text-xs text-slate-500 mt-0.5 truncate">{{ item.penulis || 'Penulis Tidak Diketahui' }}</p>
              <p class="text-xs text-slate-400 mt-1">Hal {{ item.halaman_terakhir }}</p>
            </div>
            <div class="flex items-center gap-2 shrink-0">
              <router-link :to="'/read/' + item.id" class="px-3.5 py-2 bg-indigo-600 text-white rounded-xl text-xs font-semibold hover:bg-indigo-700 transition">Baca</router-link>
              <button @click="removeActivity(item.id)" class="p-2 border border-slate-200 hover:bg-red-50 text-slate-400 hover:text-red-500 rounded-xl transition">
                <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M3 6h18M19 6v14a2 2 0 01-2 2H7a2 2 0 01-2-2V6m3 0V4a2 2 0 012-2h4a2 2 0 012 2v2"/></svg>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>