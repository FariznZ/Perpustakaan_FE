<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '../stores/authStore'

const router = useRouter()
const authStore = useAuthStore()

const isTokenSent = ref(false)
const isLoading = ref(false)
const error = ref('')
const successMessage = ref('')
const showSuccess = ref(false)

const email = ref('')
const newPassword = ref('')
const confirmPassword = ref('')

const goToLogin = () => { showSuccess.value = false; router.push('/login') }

const handleRequestToken = async () => {
  error.value = ''; successMessage.value = ''; isLoading.value = true
  const result = await authStore.forgotPassword(email.value)
  isLoading.value = false
  if (result.success) { successMessage.value = 'Email terkonfirmasi! Silakan masukkan password baru.'; isTokenSent.value = true }
  else error.value = result.message
}

const handleResetPassword = async () => {
  error.value = ''; successMessage.value = ''
  if (newPassword.value !== confirmPassword.value) { error.value = 'Konfirmasi password tidak cocok!'; return }
  isLoading.value = true
  const result = await authStore.resetPassword(newPassword.value)
  isLoading.value = false
  if (result.success) showSuccess.value = true
  else error.value = result.message
}
</script>

<template>
  <div class="min-h-screen flex items-center justify-center bg-gradient-to-br from-indigo-50 via-white to-sky-50 p-4">
    <div class="w-full max-w-md">
      <div class="text-center mb-8">
        <div class="w-14 h-14 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-2xl mx-auto flex items-center justify-center mb-5 shadow-xl shadow-indigo-200">
          <svg class="w-7 h-7 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
          </svg>
        </div>
        <h1 class="text-3xl font-bold text-slate-900 tracking-tight">{{ !isTokenSent ? 'Lupa Password' : 'Reset Password' }}</h1>
        <p class="text-slate-500 mt-1.5">{{ !isTokenSent ? 'Masukkan email terdaftar Anda' : 'Buat password baru' }}</p>
      </div>

      <div class="bg-white p-8 rounded-2xl shadow-lg shadow-slate-200/50 border border-slate-100">
        <form v-if="!isTokenSent" @submit.prevent="handleRequestToken" class="space-y-5">
          <div><label class="block text-sm font-semibold text-slate-700 mb-1.5">Email Terdaftar</label><input v-model="email" type="email" class="w-full px-4 py-2.5 rounded-xl border border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 outline-none transition bg-white/50" placeholder="contoh@email.com" required></div>
          <div v-if="error" class="text-red-600 text-sm font-medium text-center bg-red-50 p-3 rounded-xl border border-red-100">{{ error }}</div>
          <button type="submit" :disabled="isLoading" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2.5 rounded-xl transition shadow-lg shadow-indigo-200 disabled:opacity-60 flex justify-center items-center gap-2">
            <svg v-if="isLoading" class="animate-spin h-4 w-4 text-white" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
            <span>{{ isLoading ? 'Memeriksa...' : 'Konfirmasi Email' }}</span>
          </button>
        </form>

        <form v-else @submit.prevent="handleResetPassword" class="space-y-5">
          <div v-if="successMessage" class="text-emerald-600 text-sm font-medium bg-emerald-50 p-3 rounded-xl border border-emerald-200">{{ successMessage }}</div>
          <div><label class="block text-sm font-semibold text-slate-700 mb-1.5">Password Baru</label><input v-model="newPassword" type="password" class="w-full px-4 py-2.5 rounded-xl border border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 outline-none transition bg-white/50" placeholder="Min. 8 karakter" required></div>
          <div><label class="block text-sm font-semibold text-slate-700 mb-1.5">Konfirmasi</label><input v-model="confirmPassword" type="password" class="w-full px-4 py-2.5 rounded-xl border border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 outline-none transition bg-white/50" placeholder="Ulangi password" required></div>
          <div v-if="error" class="text-red-600 text-sm font-medium text-center bg-red-50 p-3 rounded-xl border border-red-100">{{ error }}</div>
          <button type="submit" :disabled="isLoading" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2.5 rounded-xl transition shadow-lg shadow-indigo-200 disabled:opacity-60 flex justify-center items-center gap-2">
            <svg v-if="isLoading" class="animate-spin h-4 w-4 text-white" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
            <span>{{ isLoading ? 'Menyimpan...' : 'Simpan Password Baru' }}</span>
          </button>
        </form>

        <div class="relative flex py-4 items-center"><div class="flex-grow border-t border-slate-100"></div><span class="flex-shrink mx-4 text-slate-400 text-xs font-medium">Navigasi</span><div class="flex-grow border-t border-slate-100"></div></div>
        <div class="flex justify-between items-center text-xs font-semibold px-1">
          <router-link to="/login" class="text-indigo-600 hover:text-indigo-700 transition">← Kembali</router-link>
          <button v-if="isTokenSent" @click="isTokenSent = false; error = ''; successMessage = ''" class="text-slate-500 hover:text-indigo-600 transition">Ganti Email</button>
        </div>
      </div>
    </div>

    <Transition enter-active-class="transition duration-300 ease-out" enter-from-class="opacity-0" enter-to-class="opacity-100"
      leave-active-class="transition duration-200 ease-in" leave-from-class="opacity-100" leave-to-class="opacity-0">
      <div v-if="showSuccess" class="fixed inset-0 z-50 flex items-center justify-center bg-slate-900/60 backdrop-blur-sm p-4">
        <div class="bg-white rounded-2xl shadow-2xl w-full max-w-sm p-8 text-center border border-slate-100 animate-pop">
          <div class="w-16 h-16 bg-emerald-100 rounded-full mx-auto flex items-center justify-center mb-5">
            <svg class="w-9 h-9 text-emerald-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5"/></svg>
          </div>
          <h2 class="text-xl font-bold text-slate-800 mb-2">Password Berhasil Diubah!</h2>
          <p class="text-sm text-slate-500 mb-6">Silakan login dengan password baru Anda.</p>
          <button @click="goToLogin" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2.5 rounded-xl transition shadow-md text-sm">Lanjut ke Login</button>
        </div>
      </div>
    </Transition>
  </div>
</template>

<style scoped>
@keyframes pop { 0% { opacity: 0; transform: scale(0.9) translateY(12px); } 100% { opacity: 1; transform: scale(1) translateY(0); } }
.animate-pop { animation: pop 0.3s ease-out; }
</style>