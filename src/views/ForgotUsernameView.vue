<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '../stores/authStore'

const router = useRouter()
const authStore = useAuthStore()

const email = ref('')
const error = ref('')
const successMessage = ref('')
const isLoading = ref(false)

const doRequestUsername = async () => {
  error.value = ''; successMessage.value = ''; isLoading.value = true
  const result = await authStore.forgotUsername(email.value)
  isLoading.value = false
  if (result.success) { successMessage.value = `Username Anda: ${result.username}`; email.value = '' }
  else error.value = result.message
}
</script>

<template>
  <div class="min-h-screen flex items-center justify-center bg-gradient-to-br from-indigo-50 via-white to-sky-50 p-4">
    <div class="w-full max-w-md">
      <div class="text-center mb-8">
        <div class="w-14 h-14 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-2xl mx-auto flex items-center justify-center mb-5 shadow-xl shadow-indigo-200">
          <svg class="w-7 h-7 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"/>
          </svg>
        </div>
        <h1 class="text-3xl font-bold text-slate-900 tracking-tight">Lupa Username</h1>
        <p class="text-slate-500 mt-1.5">Masukkan email untuk menemukan username Anda</p>
      </div>

      <div class="bg-white p-8 rounded-2xl shadow-lg shadow-slate-200/50 border border-slate-100">
        <form @submit.prevent="doRequestUsername" class="space-y-5">
          <div><label class="block text-sm font-semibold text-slate-700 mb-1.5">Email Terdaftar</label><input v-model="email" type="email" class="w-full px-4 py-2.5 rounded-xl border border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 outline-none transition bg-white/50" placeholder="contoh@email.com" required></div>
          <div v-if="error" class="text-red-600 text-sm font-medium text-center bg-red-50 p-3 rounded-xl border border-red-100">{{ error }}</div>
          <div v-if="successMessage" class="text-emerald-700 text-sm font-bold text-center bg-emerald-50 p-4 rounded-xl border-2 border-emerald-300 shadow-sm">{{ successMessage }}</div>
          <button type="submit" :disabled="isLoading" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2.5 rounded-xl transition shadow-lg shadow-indigo-200 disabled:opacity-60 flex justify-center items-center gap-2">
            <svg v-if="isLoading" class="animate-spin h-4 w-4 text-white" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
            <span>{{ isLoading ? 'Mencari...' : 'Cari Username Saya' }}</span>
          </button>
        </form>
        <div class="relative flex py-4 items-center"><div class="flex-grow border-t border-slate-100"></div><span class="flex-shrink mx-4 text-slate-400 text-xs font-medium">Navigasi</span><div class="flex-grow border-t border-slate-100"></div></div>
        <div class="flex justify-between items-center text-xs font-semibold px-1">
          <router-link to="/login" class="text-indigo-600 hover:text-indigo-700 transition">← Kembali</router-link>
          <router-link to="/register" class="text-slate-500 hover:text-indigo-600 transition">Daftar</router-link>
        </div>
      </div>
    </div>
  </div>
</template>