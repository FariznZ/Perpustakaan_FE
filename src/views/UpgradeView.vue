<script setup>
import { ref, reactive } from 'vue'
import { useAuthStore } from '../stores/authStore'
import { useRouter } from 'vue-router'
import Swal from 'sweetalert2'

const auth = useAuthStore()
const router = useRouter()

const showPaymentModal = ref(false)
const isProcessing = ref(false)
const selectedPlan = ref(null)

const plans = [
  { id: '1_month', name: 'Premium Bulanan', duration: '1 Bulan', price: 49000, originalPrice: 49000,
    features: ['Akses penuh seluruh halaman buku', 'Unduh PDF e-book', 'Membaca tanpa batas', 'Akses prioritas'],
    isPopular: false },
  { id: '5_months', name: 'Premium Hemat', duration: '5 Bulan', price: 199000, originalPrice: 245000,
    features: ['Akses penuh seluruh halaman buku', 'Unduh PDF e-book', 'Masa aktif 5 bulan', 'Hemat 20%'],
    isPopular: false },
  { id: '1_year', name: 'Premium Setahun', duration: '1 Tahun', price: 399000, originalPrice: 588000,
    features: ['Akses penuh seluruh halaman buku', 'Unduh PDF e-book', 'Masa aktif 12 bulan', 'Hemat 32%', 'Dukungan VIP'],
    isPopular: true }
]

const formData = reactive({ fullName: '', phone: '', paymentMethod: 'DANA', paymentProof: null })

const handleUpgradeClick = (plan) => {
  selectedPlan.value = plan
  formData.fullName = ''
  formData.phone = ''
  formData.paymentProof = null
  showPaymentModal.value = true
}

const handleFileChange = (e) => { if (e.target.files.length > 0) formData.paymentProof = e.target.files[0].name }

const processPayment = async () => {
  if (!formData.fullName.trim() || !formData.phone.trim()) {
    return Swal.fire({ title: 'Data Belum Lengkap', text: 'Lengkapi nama dan nomor telepon!', icon: 'warning', confirmButtonColor: '#4f46e5' })
  }
  isProcessing.value = true
  try {
    if (!auth.user?.id) throw new Error('Sesi berakhir. Silakan login ulang.')
    const result = await auth.upgradeMembership({ userId: auth.user.id, plan_id: selectedPlan.value.id, fullName: formData.fullName, phone: formData.phone })
    if (!result.success) throw new Error(result.message || 'Gagal upgrade.')
    if (auth.user) { auth.user.membership = 'premium' }
    isProcessing.value = false
    showPaymentModal.value = false
    Swal.fire({ title: 'Berhasil Premium!', html: `Paket <strong>${selectedPlan.value.name}</strong> aktif.`, icon: 'success', confirmButtonColor: '#4f46e5', timer: 3000, timerProgressBar: true }).then(() => router.push('/dashboard'))
  } catch (error) {
    isProcessing.value = false
    Swal.fire({ title: 'Upgrade Gagal', text: error.message, icon: 'error', confirmButtonColor: '#ef4444' })
  }
}
</script>

<template>
  <div>
    <div class="text-center mb-10">
      <div class="w-14 h-14 bg-gradient-to-br from-amber-400 to-orange-500 rounded-2xl mx-auto flex items-center justify-center mb-4 shadow-xl shadow-amber-200">
        <svg class="w-7 h-7 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <path d="M2 4l3 12h14l3-12-6 7-4-7-4 7-6-7zm3 16h14"/>
        </svg>
      </div>
      <h1 class="text-3xl font-bold text-slate-900 tracking-tight">Tingkatkan Pengalaman</h1>
      <p class="text-slate-500 mt-2">Pilih paket premium yang sesuai dengan kebutuhanmu</p>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-6 items-stretch">
      <div v-for="plan in plans" :key="plan.id"
        class="bg-white rounded-2xl p-6 border transition-all duration-300 flex flex-col relative card-hover"
        :class="plan.isPopular ? 'border-indigo-500 shadow-xl ring-2 ring-indigo-200 md:-translate-y-2' : 'border-slate-200'">
        <span v-if="plan.isPopular" class="absolute -top-3 left-1/2 -translate-x-1/2 bg-indigo-600 text-white text-[10px] font-bold px-3 py-1 rounded-full uppercase tracking-wider">Best Value</span>
        <div class="mb-5"><h3 class="text-lg font-bold text-slate-800">{{ plan.name }}</h3><p class="text-xs text-slate-500">{{ plan.duration }}</p></div>
        <div class="mb-5">
          <span v-if="plan.originalPrice !== plan.price" class="text-xs text-red-400 line-through font-medium">Rp {{ plan.originalPrice.toLocaleString('id-ID') }}</span>
          <div class="flex items-baseline gap-1"><span class="text-3xl font-extrabold text-slate-900">Rp {{ plan.price.toLocaleString('id-ID') }}</span></div>
        </div>
        <ul class="space-y-2.5 mb-6 flex-1">
          <li v-for="f in plan.features" :key="f" class="flex items-start gap-2.5 text-sm text-slate-600">
            <svg class="w-4 h-4 text-emerald-500 shrink-0 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M5 13l4 4L19 7"/></svg>
            <span>{{ f }}</span>
          </li>
        </ul>
        <button v-if="auth.user?.membership !== 'premium' && auth.user?.membership !== 'GOD'" @click="handleUpgradeClick(plan)"
          class="w-full py-2.5 rounded-xl font-semibold text-sm transition-all shadow-sm"
          :class="plan.isPopular ? 'bg-indigo-600 hover:bg-indigo-700 text-white shadow-indigo-200' : 'bg-indigo-50 hover:bg-indigo-100 text-indigo-600'">Upgrade</button>
        <button v-else disabled class="w-full py-2.5 bg-slate-100 text-slate-400 rounded-xl font-semibold text-sm cursor-not-allowed">Sudah Premium</button>
      </div>
    </div>

    <div v-if="showPaymentModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4">
      <div class="bg-white rounded-2xl w-full max-w-md shadow-2xl border border-slate-100 overflow-hidden">
        <div class="bg-gradient-to-r from-indigo-600 to-indigo-500 text-white p-5">
          <div class="flex justify-between items-center">
            <div><h3 class="font-bold">Form Pembayaran</h3><p class="text-xs text-indigo-200">{{ selectedPlan?.name }}</p></div>
            <button @click="showPaymentModal = false" class="text-white/70 hover:text-white"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg></button>
          </div>
        </div>
        <form @submit.prevent="processPayment" class="p-5 space-y-4">
          <div class="bg-slate-50 border border-slate-200 rounded-xl p-3 text-sm">
            <div class="flex justify-between py-1"><span class="text-slate-500">Paket</span><span class="font-semibold text-slate-800">{{ selectedPlan?.name }}</span></div>
            <div class="flex justify-between py-1 border-t border-slate-200/60 mt-1 pt-1"><span class="text-slate-500">Total</span><span class="font-bold text-indigo-600">Rp {{ selectedPlan?.price.toLocaleString('id-ID') }}</span></div>
          </div>
          <div><label class="block text-xs font-semibold text-slate-700 mb-1">Nama Lengkap</label><input v-model="formData.fullName" type="text" placeholder="Masukkan nama lengkap..." required class="w-full border border-slate-200 rounded-xl px-3 py-2.5 text-sm outline-none focus:border-indigo-500"></div>
          <div><label class="block text-xs font-semibold text-slate-700 mb-1">No. WhatsApp</label><input v-model="formData.phone" type="tel" placeholder="08xxxxxxxxxx" required class="w-full border border-slate-200 rounded-xl px-3 py-2.5 text-sm outline-none focus:border-indigo-500"></div>
          <div><label class="block text-xs font-semibold text-slate-700 mb-1">Metode Pembayaran</label>
            <select v-model="formData.paymentMethod" class="w-full border border-slate-200 rounded-xl px-3 py-2.5 text-sm bg-white outline-none focus:border-indigo-500">
              <option value="DANA">DANA</option><option value="GoPay">GoPay</option><option value="Mandiri">Transfer Bank Mandiri</option>
            </select>
          </div>
          <div><label class="block text-xs font-semibold text-slate-700 mb-1">Bukti Bayar</label>
            <div class="border-2 border-slate-200 border-dashed rounded-xl p-4 text-center bg-slate-50 cursor-pointer hover:border-indigo-400 relative">
              <input type="file" accept="image/*" @change="handleFileChange" class="absolute inset-0 opacity-0 cursor-pointer">
              <p class="text-xs text-slate-500">Klik untuk upload bukti transfer</p>
              <p v-if="formData.paymentProof" class="text-xs text-emerald-600 font-semibold mt-1">{{ formData.paymentProof }}</p>
            </div>
          </div>
          <div class="pt-2 flex justify-end gap-2 border-t border-slate-100">
            <button type="button" @click="showPaymentModal = false" :disabled="isProcessing" class="px-4 py-2 text-sm font-medium text-slate-500 hover:bg-slate-100 rounded-xl">Batal</button>
            <button type="submit" :disabled="isProcessing" class="px-5 py-2 text-sm font-semibold text-white bg-indigo-600 hover:bg-indigo-700 rounded-xl flex items-center gap-1.5 shadow-sm">
              <svg v-if="isProcessing" class="animate-spin h-3.5 w-3.5 text-white" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
              {{ isProcessing ? 'Memproses...' : 'Bayar Sekarang' }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>