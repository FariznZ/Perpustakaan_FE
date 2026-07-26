<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '../stores/authStore'

const router = useRouter()
const authStore = useAuthStore()

const name = ref('')
const username = ref('')
const email = ref('')
const phone = ref('')
const password = ref('')
const confirmPassword = ref('')

const error = ref('')
const isLoading = ref(false)
const showSuccess = ref(false)

const touched = ref({
  name: false, email: false, phone: false,
  username: false, password: false, confirmPassword: false
})

const markTouched = (field) => { touched.value[field] = true }

const nameError = computed(() => {
  if (!name.value) return 'Nama lengkap wajib diisi.'
  if (name.value.trim().length < 8) return 'Nama lengkap minimal 8 karakter.'
  return ''
})

const emailError = computed(() => {
  if (!email.value) return 'Email wajib diisi.'
  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value)) return 'Format email tidak valid.'
  return ''
})

const phoneError = computed(() => {
  if (!phone.value) return 'Nomor telepon wajib diisi.'
  if (!/^08\d{8,11}$/.test(phone.value)) return 'Nomor harus 08xx (10-13 digit).'
  return ''
})

const usernameError = computed(() => {
  if (!username.value) return 'Username wajib diisi.'
  if (username.value.length < 5) return 'Username minimal 5 karakter.'
  if (!/^[a-zA-Z0-9._]+$/.test(username.value)) return 'Hanya huruf, angka, titik, underscore.'
  return ''
})

const passwordError = computed(() => {
  if (!password.value) return 'Password wajib diisi.'
  if (password.value.length < 8) return 'Password minimal 8 karakter.'
  return ''
})

const confirmPasswordError = computed(() => {
  if (!confirmPassword.value) return 'Konfirmasi password wajib diisi.'
  if (confirmPassword.value !== password.value) return 'Konfirmasi password tidak cocok.'
  return ''
})

const isFormValid = computed(() =>
  !nameError.value && !emailError.value && !phoneError.value &&
  !usernameError.value && !passwordError.value && !confirmPasswordError.value
)

const goToLogin = () => {
  showSuccess.value = false
  router.push('/login')
}

const doRegister = async () => {
  error.value = ''
  Object.keys(touched.value).forEach(key => { touched.value[key] = true })
  if (!isFormValid.value) { error.value = 'Periksa kembali data Anda.'; return }

  isLoading.value = true
  const result = await authStore.register({ name: name.value, username: username.value, email: email.value, phone: phone.value, password: password.value })
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
            <path d="M18 9v3m0 0v3m0-3h3m-3 0h-3m-2-5a4 4 0 11-8 0 4 4 0 018 0zM3 20a6 6 0 0112 0v1H3v-1z" />
          </svg>
        </div>
        <h1 class="text-3xl font-bold text-slate-900 tracking-tight">Buat Akun Baru</h1>
        <p class="text-slate-500 mt-1.5">Daftar untuk menikmati layanan EduLibrary</p>
      </div>

      <div class="bg-white p-8 rounded-2xl shadow-lg shadow-slate-200/50 border border-slate-100">
        <form @submit.prevent="doRegister" class="space-y-4">
          <div>
            <label class="block text-sm font-semibold text-slate-700 mb-1.5">Nama Lengkap</label>
            <input v-model="name" type="text" @blur="markTouched('name')"
              class="w-full px-4 py-2.5 rounded-xl border outline-none transition text-sm bg-white/50"
              :class="touched.name && nameError ? 'border-red-400 focus:ring-2 focus:ring-red-400/30' : 'border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500'"
              placeholder="Min. 8 karakter" required>
            <p v-if="touched.name && nameError" class="text-red-500 text-xs mt-1">{{ nameError }}</p>
          </div>

          <div class="grid grid-cols-2 gap-3">
            <div>
              <label class="block text-sm font-semibold text-slate-700 mb-1.5">Email</label>
              <input v-model="email" type="email" @blur="markTouched('email')"
                class="w-full px-4 py-2.5 rounded-xl border outline-none transition text-sm bg-white/50"
                :class="touched.email && emailError ? 'border-red-400 focus:ring-2 focus:ring-red-400/30' : 'border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500'"
                placeholder="email@contoh.com" required>
              <p v-if="touched.email && emailError" class="text-red-500 text-xs mt-1">{{ emailError }}</p>
            </div>
            <div>
              <label class="block text-sm font-semibold text-slate-700 mb-1.5">No. HP</label>
              <input v-model="phone" type="text" @blur="markTouched('phone')"
                class="w-full px-4 py-2.5 rounded-xl border outline-none transition text-sm bg-white/50"
                :class="touched.phone && phoneError ? 'border-red-400 focus:ring-2 focus:ring-red-400/30' : 'border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500'"
                placeholder="08xxxxxxxxxx" required>
              <p v-if="touched.phone && phoneError" class="text-red-500 text-xs mt-1">{{ phoneError }}</p>
            </div>
          </div>

          <div>
            <label class="block text-sm font-semibold text-slate-700 mb-1.5">Username</label>
            <input v-model="username" type="text" @blur="markTouched('username')"
              class="w-full px-4 py-2.5 rounded-xl border outline-none transition text-sm bg-white/50"
              :class="touched.username && usernameError ? 'border-red-400 focus:ring-2 focus:ring-red-400/30' : 'border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500'"
              placeholder="Buat username unik..." required>
            <p v-if="touched.username && usernameError" class="text-red-500 text-xs mt-1">{{ usernameError }}</p>
          </div>

          <div class="grid grid-cols-2 gap-3">
            <div>
              <label class="block text-sm font-semibold text-slate-700 mb-1.5">Password</label>
              <input v-model="password" type="password" @blur="markTouched('password')"
                class="w-full px-4 py-2.5 rounded-xl border outline-none transition text-sm bg-white/50"
                :class="touched.password && passwordError ? 'border-red-400 focus:ring-2 focus:ring-red-400/30' : 'border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500'"
                placeholder="Min. 8 karakter" required>
              <p v-if="touched.password && passwordError" class="text-red-500 text-xs mt-1">{{ passwordError }}</p>
            </div>
            <div>
              <label class="block text-sm font-semibold text-slate-700 mb-1.5">Konfirmasi</label>
              <input v-model="confirmPassword" type="password" @blur="markTouched('confirmPassword')"
                class="w-full px-4 py-2.5 rounded-xl border outline-none transition text-sm bg-white/50"
                :class="touched.confirmPassword && confirmPasswordError ? 'border-red-400 focus:ring-2 focus:ring-red-400/30' : 'border-slate-300 focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500'"
                placeholder="••••••••" required>
              <p v-if="touched.confirmPassword && confirmPasswordError" class="text-red-500 text-xs mt-1">{{ confirmPasswordError }}</p>
            </div>
          </div>

          <div v-if="error" class="text-red-600 text-sm font-medium text-center bg-red-50 p-3 rounded-xl border border-red-100">
            {{ error }}
          </div>

          <button type="submit" :disabled="isLoading"
            class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2.5 rounded-xl transition shadow-lg shadow-indigo-200 disabled:opacity-60 flex justify-center items-center gap-2">
            <svg v-if="isLoading" class="animate-spin h-4 w-4 text-white" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <span>{{ isLoading ? 'Mendaftarkan...' : 'Daftar Sekarang' }}</span>
          </button>
        </form>

        <div class="relative flex py-4 items-center">
          <div class="flex-grow border-t border-slate-100"></div>
          <span class="flex-shrink mx-4 text-slate-400 text-xs font-medium">Sudah punya akun?</span>
          <div class="flex-grow border-t border-slate-100"></div>
        </div>

        <div class="text-center">
          <router-link to="/login" class="font-semibold text-indigo-600 hover:text-indigo-700 transition text-sm">
            Masuk di Sini
          </router-link>
        </div>
      </div>
    </div>

    <Transition enter-active-class="transition duration-300 ease-out" enter-from-class="opacity-0" enter-to-class="opacity-100"
      leave-active-class="transition duration-200 ease-in" leave-from-class="opacity-100" leave-to-class="opacity-0">
      <div v-if="showSuccess" class="fixed inset-0 z-50 flex items-center justify-center bg-slate-900/60 backdrop-blur-sm p-4">
        <div class="bg-white rounded-2xl shadow-2xl w-full max-w-sm p-8 text-center border border-slate-100 animate-pop">
          <div class="w-16 h-16 bg-emerald-100 rounded-full mx-auto flex items-center justify-center mb-5">
            <svg class="w-9 h-9 text-emerald-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
              <path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" />
            </svg>
          </div>
          <h2 class="text-xl font-bold text-slate-800 mb-2">Registrasi Berhasil!</h2>
          <p class="text-sm text-slate-500 mb-6">Akun Anda telah dibuat. Silakan masuk untuk mulai menggunakan EduLibrary.</p>
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