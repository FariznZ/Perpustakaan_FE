<script setup>
import { ref, computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useAuthStore } from '../stores/authStore'
import { useBookStore } from '../stores/bookStore'
import { useLoanStore } from '../stores/loanStore'
import Icon from '../components/Icon.vue'

const route = useRoute()
const router = useRouter()
const auth = useAuthStore()
const bookStore = useBookStore()
const loanStore = useLoanStore()

const book = computed(() => bookStore.books.find(b => b.id === parseInt(route.params.id) || b.id === route.params.id))
const msg = ref('')

const hasFullAccess = computed(() => ['premium', 'admin', 'GOD'].includes(auth.user?.membership))

const isDigital = computed(() => book.value && ((book.value.pdf_buku && book.value.pdf_buku !== 'null' && book.value.pdf_buku !== '') || book.value.type === 'digital'))

const isPending = computed(() => loanStore.loans.some(l => String(l.siswa_id) === String(auth.user?.id) && String(l.buku_id) === String(book.value?.id) && l.status === 'pending'))

const isBorrowedActive = computed(() => loanStore.loans.some(l => String(l.siswa_id) === String(auth.user?.id) && String(l.buku_id) === String(book.value?.id) && l.status === 'dipinjam'))

const handleAction = async () => {
  if (!book.value) return
  if (isDigital.value) { router.push('/read/' + book.value.id); return }
  
  const currentStock = book.value.stok !== undefined ? book.value.stok : book.value.stock
  if (currentStock <= 0) { msg.value = 'Stok buku ini sudah habis.'; setTimeout(() => msg.value = '', 4000); return }
  if (isPending.value || isBorrowedActive.value) { msg.value = 'Kamu sedang meminjam atau mengajukan buku ini.'; setTimeout(() => msg.value = '', 4000); return }

  const success = await loanStore.requestLoan(auth.user?.id, book.value.id)
  msg.value = success ? 'Permintaan berhasil dikirim. Menunggu persetujuan Admin.' : 'Permintaan gagal diproses.'
  setTimeout(() => msg.value = '', 4000)
}

const downloadPDF = async () => {
  if (!hasFullAccess.value) { router.push('/upgrade'); return }
  if (!book.value?.pdf_buku) { alert('File PDF tidak ditemukan.'); return }
  try {
    const response = await fetch(book.value.pdf_buku)
    const blob = await response.blob()
    const url = window.URL.createObjectURL(blob)
    const link = document.createElement('a')
    link.href = url
    link.setAttribute('download', `${book.value.nama_buku || book.value.title || 'Buku'}.pdf`)
    document.body.appendChild(link)
    link.click()
    document.body.removeChild(link)
    window.URL.revokeObjectURL(url)
  } catch { window.open(book.value.pdf_buku, '_blank') }
}

const reviewText = ref('')
const reviewRating = ref(5)

const submitReview = () => {
  if (reviewText.value.trim() && book.value) {
    if (bookStore.addReview(book.value.id, auth.user?.name || auth.user?.username || 'gratis', reviewRating.value, reviewText.value)) {
      reviewText.value = ''
    }
  }
}

const formatDate = (ds) => {
  if (!ds) return 'Baru-baru ini'
  const d = new Date(ds)
  return isNaN(d.getTime()) ? 'Baru-baru ini' : d.toLocaleDateString('id-ID', { day: 'numeric', month: 'long', year: 'numeric' })
}
</script>

<template>
  <div v-if="book" class="bg-white rounded-2xl border border-slate-200 overflow-hidden">
    <div class="p-6 lg:p-8 flex flex-col md:flex-row gap-8">
      <div class="w-full md:w-72 shrink-0">
        <img :src="book.foto_buku || book.cover" alt="Cover" class="w-full rounded-2xl shadow-lg border border-slate-100 aspect-[3/4] object-cover bg-slate-200">
        
        <div class="flex flex-col gap-2.5 mt-5">
          <button v-if="isDigital" @click="handleAction" class="w-full py-3 px-4 rounded-xl font-semibold text-white transition shadow-sm flex items-center justify-center gap-2 bg-indigo-600 hover:bg-indigo-700">
            <Icon name="monitor" size="20" /> Baca Online
          </button>

          <template v-else>
            <button v-if="isBorrowedActive" disabled class="w-full py-3 px-4 rounded-xl font-semibold text-indigo-600 bg-indigo-50 cursor-not-allowed flex items-center justify-center gap-2">
              <Icon name="bookmark" size="20" /> Sedang Dipinjam
            </button>
            <button v-else-if="isPending" disabled class="w-full py-3 px-4 rounded-xl font-semibold text-amber-600 bg-amber-50 cursor-not-allowed flex items-center justify-center gap-2">
              <Icon name="monitor" size="20" /> Menunggu
            </button>
            <button v-else @click="handleAction" :disabled="(book.stok !== undefined ? book.stok : book.stock) <= 0" 
              class="w-full py-3 px-4 rounded-xl font-semibold text-white transition shadow-sm flex items-center justify-center gap-2"
              :class="(book.stok !== undefined ? book.stok : book.stock) > 0 ? 'bg-indigo-600 hover:bg-indigo-700' : 'bg-slate-300 cursor-not-allowed'">
              <Icon name="book" size="20" /> Pinjam Buku
            </button>
          </template>

          <div v-if="!isDigital" class="p-3 rounded-xl text-sm font-medium text-center border"
            :class="(book.stok !== undefined ? book.stok : book.stock) > 0 ? 'bg-emerald-50 text-emerald-700 border-emerald-200' : 'bg-red-50 text-red-600 border-red-200'">
            {{ (book.stok !== undefined ? book.stok : book.stock) > 0 ? 'Stok: ' + (book.stok !== undefined ? book.stok : book.stock) : 'Stok Habis' }}
          </div>

          <button v-if="isDigital" @click="downloadPDF" class="w-full py-3 px-4 rounded-xl font-semibold transition border flex items-center justify-center gap-2"
            :class="hasFullAccess ? 'bg-white text-indigo-600 border-indigo-200 hover:bg-indigo-50' : 'bg-slate-50 text-slate-400 border-slate-200'">
            <Icon name="download" size="20" /> {{ hasFullAccess ? 'Unduh PDF' : 'Unduh (Premium)' }}
          </button>
        </div>

        <div v-if="msg" class="mt-3 text-sm text-center font-medium p-3 rounded-xl" 
          :class="msg.includes('berhasil') ? 'bg-emerald-50 text-emerald-700 border border-emerald-200' : 'bg-red-50 text-red-600 border border-red-200'">{{ msg }}</div>
      </div>
      
      <div class="flex-grow min-w-0">
        <div class="flex items-center gap-2 mb-3 flex-wrap">
          <span class="px-2.5 py-1 rounded-lg bg-slate-100 text-slate-600 text-xs font-semibold uppercase tracking-wider">{{ book.jenis_buku || book.category }}</span>
          <span class="px-2.5 py-1 rounded-lg text-xs font-semibold uppercase tracking-wider"
            :class="isDigital ? 'bg-indigo-50 text-indigo-700' : 'bg-amber-50 text-amber-700'">{{ isDigital ? 'E-Book' : 'Fisik' }}</span>
        </div>
        <h1 class="text-2xl md:text-3xl font-bold text-slate-900 mb-1">{{ book.nama_buku || book.title }}</h1>
        <p class="text-base text-slate-500 mb-5">oleh <span class="font-medium text-slate-700">{{ book.penulis || book.author || 'Penulis Tidak Diketahui' }}</span></p>
        
        <div class="flex items-center gap-1.5 text-amber-400 mb-6 bg-amber-50 w-max px-3.5 py-2 rounded-xl border border-amber-100">
          <Icon name="star" size="18" />
          <span class="font-bold text-amber-600">{{ book.rating || '0.0' }}</span>
          <span class="text-amber-500/60 text-sm">({{ book.reviews?.length || 0 }} ulasan)</span>
        </div>
        
        <h3 class="text-base font-bold text-slate-800 mb-2">Sinopsis</h3>
        <p class="text-slate-600 leading-relaxed">{{ book.deskripsi || book.description || 'Tidak ada deskripsi.' }}</p>
      </div>
    </div>

    <div class="border-t border-slate-100 bg-slate-50/50 p-6 lg:p-8">
      <h3 class="text-lg font-bold text-slate-800 mb-6">Ulasan Pembaca</h3>
      
      <div v-if="auth.user?.role === 'user'" class="bg-white p-5 rounded-2xl border border-slate-200 mb-8">
        <h4 class="font-semibold text-slate-700 mb-3">Tulis ulasan</h4>
        <textarea v-model="reviewText" rows="3" class="w-full border border-slate-200 rounded-xl p-3 text-sm focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 outline-none mb-3 resize-none" placeholder="Bagaimana pendapat Anda tentang buku ini?"></textarea>
        <div class="flex justify-between items-center">
          <select v-model="reviewRating" class="border border-slate-200 rounded-xl p-2 text-sm bg-white outline-none focus:ring-2 focus:ring-indigo-500/30">
            <option :value="5">5 - Sangat Bagus</option>
            <option :value="4">4 - Bagus</option>
            <option :value="3">3 - Cukup</option>
            <option :value="2">2 - Kurang</option>
            <option :value="1">1 - Buruk</option>
          </select>
          <button @click="submitReview" :disabled="!reviewText" class="bg-indigo-600 text-white px-5 py-2 rounded-xl text-sm font-semibold hover:bg-indigo-700 disabled:opacity-50 transition">Kirim</button>
        </div>
      </div>

      <div class="space-y-3">
        <div v-for="(rev, idx) in book.reviews" :key="idx" class="bg-white p-4 rounded-xl border border-slate-200">
          <div class="flex items-center gap-3 mb-2">
            <div class="w-8 h-8 bg-indigo-100 text-indigo-600 rounded-full flex items-center justify-center font-bold text-sm uppercase">{{ rev.user.charAt(0) }}</div>
            <div>
              <div class="font-semibold text-slate-800 text-sm">{{ rev.user }}</div>
              <div class="flex text-amber-400 text-xs">
                <span v-for="n in 5" :key="n" :class="n <= rev.rating ? 'text-amber-400' : 'text-slate-200'">★</span>
              </div>
            </div>
            <div class="ml-auto text-xs text-slate-400">{{ formatDate(rev.created_at || rev.createdAt || rev.date) }}</div>
          </div>
          <p class="text-slate-600 text-sm pl-11">{{ rev.text }}</p>
        </div>
        <div v-if="!book.reviews || book.reviews.length === 0" class="text-slate-500 text-center py-6 text-sm bg-white rounded-xl border border-slate-200">Belum ada ulasan. Jadilah yang pertama!</div>
      </div>
    </div>
  </div>
</template>