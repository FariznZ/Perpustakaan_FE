<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '../stores/authStore'

const auth = useAuthStore()
const router = useRouter()
const username = ref('')
const password = ref('')
const error = ref('')
const isLoading = ref(false)

const doLogin = async () => {
  isLoading.value = true
  error.value = ''
  const result = await auth.login(username.value, password.value)
  if (result.success) {
     router.push('/dashboard')
  } else {
     error.value = result.message || 'Username atau password salah'
  }
  isLoading.value = false
}
</script>

<template>
  <div class="min-h-screen flex items-center justify-center bg-gradient-to-br from-indigo-50 via-white to-sky-50 p-4">
    <div class="w-full max-w-md">
      <div class="text-center mb-8">
        <div class="w-14 h-14 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-2xl mx-auto flex items-center justify-center mb-5 shadow-xl shadow-indigo-200">
          <svg class="w-7 h-7 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"/>
            <path d="M12 2v20"/>
          </svg>
        </div>
        <h1 class="text-3xl font-bold text-slate-900 tracking-tight">Selamat Datang Kembali</h1>
        <p class="text-slate-500 mt-1.5">Masuk ke akun EduLibrary Anda</p>
      </div>

      <div class="bg-white p-8 rounded-2xl shadow-lg shadow-slate-200/50 border border-slate-100">
        <form @submit.prevent="doLogin" class="space-y-5">
          <div>
            <label class="block text-sm font-semibold text-slate-700 mb-1.5">Username</label>
            <input 
              v-model="username" 
              type="text" 
              class="w-full px-4 py-2.5 rounded-xl border border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 outline-none transition bg-white/50" 
              placeholder="Masukkan username..." 
              required
            >
          </div>
          
          <div>
            <div class="flex justify-between items-center mb-1.5">
              <label class="text-sm font-semibold text-slate-700">Password</label>
              <router-link to="/forgot-password" class="text-xs font-medium text-indigo-600 hover:text-indigo-700 transition">
                Lupa Password?
              </router-link>
            </div>
            <input 
              v-model="password" 
              type="password" 
              class="w-full px-4 py-2.5 rounded-xl border border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 outline-none transition bg-white/50" 
              placeholder="••••••••" 
              required
            >
          </div>

          <div v-if="error" class="text-red-600 text-sm font-medium text-center bg-red-50 p-3 rounded-xl border border-red-100">
            {{ error }}
          </div>

          <button 
            type="submit" 
            :disabled="isLoading" 
            class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2.5 rounded-xl transition shadow-lg shadow-indigo-200 disabled:opacity-60 flex justify-center items-center gap-2"
          >
            <svg v-if="isLoading" class="animate-spin h-4 w-4 text-white" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <span>{{ isLoading ? 'Memproses...' : 'Masuk' }}</span>
          </button>
        </form>

        <div class="relative flex py-5 items-center">
          <div class="flex-grow border-t border-slate-100"></div>
          <span class="flex-shrink mx-4 text-slate-400 text-xs font-medium">atau</span>
          <div class="flex-grow border-t border-slate-100"></div>
        </div>

        <div class="text-center space-y-2">
          <p class="text-sm text-slate-600">
            Belum punya akun? 
            <router-link to="/register" class="font-semibold text-indigo-600 hover:text-indigo-700 transition">
              Daftar Sekarang
            </router-link>
          </p>
          <router-link to="/forgot-username" class="text-xs text-slate-400 hover:text-indigo-600 transition inline-block">
            Lupa Username?
          </router-link>
        </div>
      </div>
    </div>
  </div>
</template>