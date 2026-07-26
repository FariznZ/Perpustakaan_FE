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
const showPassword = ref(false)

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
  <div class="min-h-screen lg:flex bg-slate-50">
    <!-- LEFT: Brand Panel -->
    <div class="relative lg:w-[480px] flex-shrink-0 bg-gradient-to-br from-indigo-950 via-indigo-900 to-violet-950 p-10 lg:p-14 flex flex-col justify-between overflow-hidden min-h-[320px] lg:min-h-screen">
      <!-- Decorative floating blobs -->
      <div class="absolute -top-32 -right-32 w-[28rem] h-[28rem] bg-indigo-400/10 rounded-full blur-3xl animate-float-slow"></div>
      <div class="absolute -bottom-24 -left-24 w-[22rem] h-[22rem] bg-violet-400/10 rounded-full blur-3xl animate-float" style="animation-delay: 2s"></div>
      <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-80 h-80 bg-sky-400/5 rounded-full blur-3xl animate-float-slow" style="animation-delay: 4s"></div>

      <!-- Subtle dot pattern overlay -->
      <svg class="absolute inset-0 w-full h-full opacity-[0.03] pointer-events-none" xmlns="http://www.w3.org/2000/svg">
        <defs>
          <pattern id="dots-lg" patternUnits="userSpaceOnUse" width="32" height="32">
            <circle cx="2" cy="2" r="1.5" fill="white"/>
          </pattern>
        </defs>
        <rect width="100%" height="100%" fill="url(#dots-lg)"/>
      </svg>

      <!-- Content -->
      <div class="relative z-10">
        <div class="flex items-center gap-3">
          <div class="w-11 h-11 bg-white/15 backdrop-blur-sm rounded-xl flex items-center justify-center ring-1 ring-white/20">
            <svg class="w-6 h-6 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
              <path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"/>
              <path d="M12 2v20"/>
            </svg>
          </div>
          <span class="text-white font-bold text-lg tracking-tight">EduLibrary</span>
        </div>
      </div>

      <!-- Center hero visual (hidden on mobile) -->
      <div class="relative z-10 hidden lg:block">
        <div class="flex flex-col items-center text-center">
          <!-- 3D Book Render -->
          <div class="relative mb-8 flex items-center justify-center" style="height: 290px">
            <div class="scene">
              <div class="book-wrapper">
                <div class="book-body">
                  <div class="face face-back"></div>
                  <div class="face face-spine">
                    <div class="spine-text">EDUBOOK</div>
                  </div>
                  <div class="face face-side">
                    <div class="page-line" style="top: 18%"></div>
                    <div class="page-line" style="top: 34%"></div>
                    <div class="page-line" style="top: 50%"></div>
                    <div class="page-line" style="top: 66%"></div>
                    <div class="page-line" style="top: 82%"></div>
                  </div>
                  <div class="face face-top">
                    <div class="page-line" style="top: 28%"></div>
                    <div class="page-line" style="top: 56%"></div>
                  </div>
                  <div class="face face-bottom"></div>
                  <div class="face face-inner">
                    <div class="inner-text">EduLibrary</div>
                  </div>
                </div>
                <div class="book-front-cover">
                  <div class="front-cover-face">
                    <div class="cover-highlight"></div>
                    <div class="book-title">EDUBOOK</div>
                    <div class="book-line"></div>
                    <div class="book-subtitle">Digital Library</div>
                  </div>
                </div>
                <div class="book-page page-left"></div>
                <div class="book-page page-right"></div>
              </div>
              <div class="book-shadow"></div>
              <div class="book-glow"></div>
            </div>
          </div>

          <h2 class="text-white font-bold text-2xl tracking-tight mb-3">Perpustakaan Digital</h2>
          <p class="text-indigo-200/70 text-sm leading-relaxed max-w-xs mx-auto">
            Akses ribuan buku digital kapan saja, di mana saja. Temukan pengetahuan tanpa batas.
          </p>
        </div>
      </div>

      <!-- Foreground particles -->
      <div class="absolute inset-0 z-20 pointer-events-none">
        <svg class="w-full h-full" viewBox="0 0 480 600" fill="none" xmlns="http://www.w3.org/2000/svg">
          <circle cx="70" cy="100" r="2.5" fill="white" opacity="0.35" class="particle"/>
          <circle cx="410" cy="150" r="2" fill="white" opacity="0.25" class="particle" style="animation-delay: 0.3s"/>
          <circle cx="360" cy="440" r="3" fill="white" opacity="0.2" class="particle" style="animation-delay: 0.8s"/>
          <circle cx="50" cy="470" r="2" fill="white" opacity="0.3" class="particle" style="animation-delay: 0.5s"/>
          <circle cx="200" cy="260" r="1.5" fill="white" opacity="0.35" class="particle" style="animation-delay: 1.1s"/>
          <circle cx="130" cy="370" r="2.5" fill="white" opacity="0.2" class="particle" style="animation-delay: 0.6s"/>
          <circle cx="390" cy="300" r="1.5" fill="white" opacity="0.3" class="particle" style="animation-delay: 0.9s"/>
          <circle cx="260" cy="90" r="2" fill="white" opacity="0.25" class="particle" style="animation-delay: 0.2s"/>
          <circle cx="440" cy="520" r="1.5" fill="white" opacity="0.2" class="particle" style="animation-delay: 1.3s"/>
          <circle cx="30" cy="280" r="2" fill="white" opacity="0.25" class="particle" style="animation-delay: 0.7s"/>
        </svg>
      </div>

      <!-- Bottom quote/stat -->
      <div class="relative z-10 hidden lg:block">
        <div class="border-t border-white/10 pt-6">
          <div class="flex items-center gap-3">
            <div class="flex -space-x-2">
              <div class="w-8 h-8 rounded-full bg-indigo-400/30 ring-2 ring-indigo-900 flex items-center justify-center text-[10px] font-bold text-white">A</div>
              <div class="w-8 h-8 rounded-full bg-violet-400/30 ring-2 ring-indigo-900 flex items-center justify-center text-[10px] font-bold text-white">B</div>
              <div class="w-8 h-8 rounded-full bg-sky-400/30 ring-2 ring-indigo-900 flex items-center justify-center text-[10px] font-bold text-white">C</div>
            </div>
            <div class="text-xs text-indigo-200/60">
              <span class="text-white font-semibold">10,000+</span> siswa telah bergabung
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- RIGHT: Form Panel -->
    <div class="flex-1 flex items-center justify-center p-6 lg:p-12">
      <div class="w-full max-w-sm">
        <!-- Mobile brand (visible only on small screens) -->
        <div class="flex items-center gap-3 mb-10 lg:hidden">
          <div class="w-10 h-10 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-xl flex items-center justify-center shadow-lg shadow-indigo-200">
            <svg class="w-5 h-5 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"/>
              <path d="M12 2v20"/>
            </svg>
          </div>
          <span class="font-bold text-lg text-slate-800 tracking-tight">EduLibrary</span>
        </div>

        <!-- Welcome text -->
        <div class="mb-8">
          <h1 class="text-3xl font-bold text-slate-900 tracking-tight">Selamat Datang Kembali</h1>
          <p class="text-slate-500 mt-2">Masuk untuk melanjutkan ke perpustakaan Anda</p>
        </div>

        <!-- Form -->
        <form @submit.prevent="doLogin" class="space-y-5">
          <div class="space-y-1.5">
            <label class="block text-sm font-semibold text-slate-700">Username</label>
            <input
              v-model="username"
              type="text"
              class="w-full px-4 py-3 rounded-xl border border-slate-300 focus:ring-2 focus:ring-indigo-500/25 focus:border-indigo-500 outline-none transition bg-white shadow-sm text-sm"
              placeholder="Masukkan username"
              required
            >
          </div>

          <div class="space-y-1.5">
            <div class="flex justify-between items-center">
              <label class="text-sm font-semibold text-slate-700">Password</label>
              <router-link to="/forgot-password" class="text-xs font-medium text-indigo-600 hover:text-indigo-700 transition">
                Lupa Password?
              </router-link>
            </div>
            <div class="relative">
              <input
                v-model="password"
                :type="showPassword ? 'text' : 'password'"
                class="w-full px-4 py-3 pr-11 rounded-xl border border-slate-300 focus:ring-2 focus:ring-indigo-500/25 focus:border-indigo-500 outline-none transition bg-white shadow-sm text-sm"
                placeholder="Masukkan password"
                required
              >
              <button
                type="button"
                @click="showPassword = !showPassword"
                class="absolute right-3 top-1/2 -translate-y-1/2 p-1 text-slate-400 hover:text-slate-600 transition"
                tabindex="-1"
              >
                <svg v-if="!showPassword" class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M3 3l18 18M12 5c-4.97 0-9 3.5-11 7a12.3 12.3 0 004.2 4.8M8.5 8.5A4 4 0 0116.5 12M5.2 16.8A8.5 8.5 0 0121 12a8.5 8.5 0 01-6.2 5.8"/>
                  <path stroke-linecap="round" stroke-linejoin="round" d="M12 16a4 4 0 01-3.5-2.1"/>
                </svg>
                <svg v-else class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M12 5c-4.97 0-9 3.5-11 7a12.3 12.3 0 004.2 4.8M12 19c4.97 0 9-3.5 11-7a12.3 12.3 0 00-4.2-4.8"/>
                  <circle cx="12" cy="12" r="3"/>
                </svg>
              </button>
            </div>
          </div>

          <div v-if="error" class="text-red-600 text-sm font-medium text-center bg-red-50 p-3 rounded-xl border border-red-100">
            {{ error }}
          </div>

          <button
            type="submit"
            :disabled="isLoading"
            class="w-full bg-indigo-600 hover:bg-indigo-700 active:bg-indigo-800 text-white font-semibold py-3 rounded-xl transition shadow-lg shadow-indigo-200/50 disabled:opacity-60 disabled:cursor-not-allowed flex justify-center items-center gap-2 text-sm"
          >
            <svg v-if="isLoading" class="animate-spin h-4 w-4 text-white" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <span>{{ isLoading ? 'Memproses...' : 'Masuk' }}</span>
          </button>
        </form>

        <!-- Divider -->
        <div class="relative flex py-6 items-center">
          <div class="flex-grow border-t border-slate-200"></div>
          <span class="flex-shrink mx-4 text-slate-400 text-xs font-medium">atau</span>
          <div class="flex-grow border-t border-slate-200"></div>
        </div>

        <!-- Footer links -->
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

<style scoped>
@keyframes float {
  0%, 100% { transform: translateY(0px) rotate(0deg); }
  50% { transform: translateY(-18px) rotate(0.5deg); }
}
@keyframes float-slow {
  0%, 100% { transform: translateY(0px); }
  50% { transform: translateY(-12px); }
}
.animate-float { animation: float 8s ease-in-out infinite; }
.animate-float-slow { animation: float-slow 12s ease-in-out infinite; }

@keyframes pulse-slow {
  0%, 100% { opacity: 0.3; }
  50% { opacity: 0.6; }
}
.animate-pulse-slow { animation: pulse-slow 4s ease-in-out infinite; }

@keyframes particle-glow {
  0%, 100% { opacity: 0.2; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(1.3); }
}
.particle { animation: particle-glow 3s ease-in-out infinite; }

/* 3D Book Render */
.scene {
  perspective: 1000px;
  width: 200px;
  height: 300px;
  position: relative;
}
.book-wrapper {
  position: absolute;
  width: 180px;
  height: 260px;
  transform-style: preserve-3d;
  transform: rotateX(-14deg) rotateY(-22deg);
  left: 50%;
  top: 50%;
  margin-left: -90px;
  margin-top: -130px;
}
.book-body {
  position: absolute;
  width: 180px;
  height: 260px;
  transform-style: preserve-3d;
}
.face {
  position: absolute;
  backface-visibility: hidden;
}
.front-cover-face {
  width: 180px;
  height: 260px;
  background: linear-gradient(165deg, #7ec8ff, #5b9cf7 25%, #3b82f6 55%, #5b9cf7 85%, #7ec8ff);
  border-radius: 16px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
  box-shadow:
    inset 0 1px 0 rgba(255,255,255,0.15),
    inset 0 -2px 0 rgba(0,0,0,0.15);
}
.cover-highlight {
  position: absolute;
  top: -10%;
  left: -10%;
  width: 120%;
  height: 120%;
  background: radial-gradient(ellipse at 30% 20%, rgba(255,255,255,0.25) 0%, transparent 55%);
  pointer-events: none;
}
.face-inner {
  width: 180px;
  height: 260px;
  transform: translateZ(15px);
  background: linear-gradient(175deg, #f0ece4, #e8e4da);
  border-radius: 3px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.inner-text {
  font-size: 11px;
  font-weight: 600;
  letter-spacing: 2px;
  color: rgba(0,0,0,0.12);
  text-transform: uppercase;
}
.face-back {
  width: 180px;
  height: 260px;
  transform: rotateY(180deg) translateZ(15px);
  background: linear-gradient(180deg, #162952, #122347);
}
.face-spine {
  width: 30px;
  height: 260px;
  transform: rotateY(-90deg) translateZ(90px);
  background: linear-gradient(180deg, #0d1d3a, #0a1830);
  border-radius: 2px 0 0 2px;
}
.face-side {
  width: 30px;
  height: 260px;
  transform: rotateY(90deg) translateZ(90px);
  background: linear-gradient(90deg, #e0dcd2, #e8e4da 60%, #f0ece4);
}
.face-top {
  width: 180px;
  height: 30px;
  transform: rotateX(-90deg) translateZ(130px);
  background: linear-gradient(180deg, #f5f0e8, #ece8e0);
}
.face-bottom {
  width: 180px;
  height: 30px;
  transform: rotateX(90deg) translateZ(130px);
  background: #c4c0b8;
}
.book-title {
  font-size: 22px;
  font-weight: 900;
  letter-spacing: 7px;
  background: linear-gradient(180deg, #f5e6b8, #d4a843 40%, #b8902a);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  filter: drop-shadow(0 1px 2px rgba(0,0,0,0.3));
}
.book-line {
  width: 60px;
  height: 1.5px;
  background: linear-gradient(90deg, transparent, rgba(212,168,67,0.5), transparent);
  margin: 12px 0;
}
.book-subtitle {
  font-size: 10px;
  font-weight: 400;
  letter-spacing: 3px;
  color: rgba(255,255,255,0.4);
  text-transform: uppercase;
}
.spine-text {
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 3px;
  color: rgba(255,255,255,0.5);
  writing-mode: vertical-lr;
  text-orientation: mixed;
  transform: rotate(180deg);
  white-space: nowrap;
  position: absolute;
  bottom: 20px;
  left: 50%;
  margin-left: -5px;
}
.page-line {
  position: absolute;
  left: 8%;
  width: 84%;
  height: 1px;
  background: rgba(0,0,0,0.06);
}
.book-shadow {
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%) rotateX(80deg);
  width: 200px;
  height: 50px;
  background: radial-gradient(ellipse at center, rgba(0,0,0,0.25) 0%, transparent 70%);
  filter: blur(15px);
  pointer-events: none;
}
.book-glow {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 250px;
  height: 250px;
  background: radial-gradient(ellipse at center, rgba(59,130,246,0.12) 0%, transparent 60%);
  pointer-events: none;
}

.book-front-cover {
  position: absolute;
  width: 180px;
  height: 260px;
  transform-style: preserve-3d;
  transform: translateZ(15px);
  transform-origin: 0 50%;
  will-change: transform;
  animation: cover-open 5s cubic-bezier(0.22, 0.61, 0.36, 1) forwards;
}

.book-page {
  position: absolute;
  width: 75px;
  height: 240px;
  top: 10px;
  background: linear-gradient(180deg, #ffffff, #f5f3ef);
  border-radius: 3px;
  box-shadow: 0 0 4px rgba(0,0,0,0.04);
  transform-style: preserve-3d;
  will-change: transform;
  opacity: 0;
}
.page-left {
  left: -75px;
  transform-origin: right center;
  transform: translateZ(15.8px) rotateY(0deg);
  animation: page-v-left 4s 1.8s cubic-bezier(0.22, 0.61, 0.36, 1) forwards;
}
.page-right {
  left: 0;
  transform-origin: left center;
  transform: translateZ(15.8px) rotateY(0deg);
  animation: page-v-right 4s 1.8s cubic-bezier(0.22, 0.61, 0.36, 1) forwards;
}

@keyframes cover-open {
  0% { transform: translateZ(15px) rotateY(0deg); }
  100% { transform: translateZ(15px) rotateY(-75deg); }
}
@keyframes page-v-left {
  0% { transform: translateZ(15.8px) rotateY(0deg); opacity: 0; }
  25% { opacity: 1; }
  100% { transform: translateZ(15.8px) rotateY(-22deg); opacity: 1; }
}
@keyframes page-v-right {
  0% { transform: translateZ(15.8px) rotateY(0deg); opacity: 0; }
  25% { opacity: 1; }
  100% { transform: translateZ(15.8px) rotateY(22deg); opacity: 1; }
}
</style>
