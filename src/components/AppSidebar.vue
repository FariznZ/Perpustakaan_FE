<script setup>
import { computed } from 'vue'
import { useAuthStore } from '../stores/authStore'
import { useRouter } from 'vue-router'
import Icon from './Icon.vue'

defineProps({ open: Boolean })
defineEmits(['close'])

const auth = useAuthStore()
const router = useRouter()

const isAdmin = computed(() =>
  auth.user?.role?.toLowerCase() === 'admin'
)

const handleLogout = async () => {
  await auth.logout()
  router.push('/login')
}
</script>

<template>
  <aside
    class="w-64 bg-white border-r border-slate-200 flex flex-col fixed left-0 top-0 z-40 transition-transform duration-300 -translate-x-full lg:translate-x-0 overflow-y-auto lg:min-h-screen max-h-[100dvh] pb-4"
    :class="{ 'translate-x-0': open }"
  >
    <div class="p-5">
      <div class="flex items-center gap-2.5 mb-8 px-1">
        <div class="w-9 h-9 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-xl flex items-center justify-center shadow-md shadow-indigo-200">
          <svg class="w-5 h-5 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"/>
            <path d="M12 2v20"/>
          </svg>
        </div>
        <span class="text-lg font-bold text-slate-800">EduLibrary</span>
      </div>

      <div class="space-y-1 mb-6">
        <p class="text-[11px] uppercase font-semibold text-slate-400 mb-2 px-3 tracking-wider">Menu Utama</p>
        <router-link to="/dashboard" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-slate-600 hover:bg-indigo-50 hover:text-indigo-600 transition-all duration-200 font-medium" active-class="!bg-indigo-50 !text-indigo-600 !font-semibold" @click="$emit('close')">
          <Icon name="monitor" size="18" /> Beranda
        </router-link>
        <router-link to="/catalog" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-slate-600 hover:bg-indigo-50 hover:text-indigo-600 transition-all duration-200 font-medium" active-class="!bg-indigo-50 !text-indigo-600 !font-semibold" @click="$emit('close')">
          <Icon name="search" size="18" /> Katalog
        </router-link>
      </div>

      <div v-if="!isAdmin" class="space-y-1 mb-6">
        <p class="text-[11px] uppercase font-semibold text-slate-400 mb-2 px-3 tracking-wider">Aktivitas Saya</p>
        <router-link to="/history" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-slate-600 hover:bg-indigo-50 hover:text-indigo-600 transition-all duration-200 font-medium" active-class="!bg-indigo-50 !text-indigo-600 !font-semibold" @click="$emit('close')">
          <Icon name="bookmark" size="18" /> Riwayat
        </router-link>
        <router-link to="/upgrade" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-slate-600 hover:bg-amber-50 hover:text-amber-600 transition-all duration-200 font-medium" active-class="!bg-amber-50 !text-amber-600 !font-semibold" @click="$emit('close')">
          <Icon name="crown" size="18" /> Membership
        </router-link>
      </div>

      <div v-if="isAdmin" class="space-y-1 mb-6">
        <p class="text-[11px] uppercase font-semibold text-slate-400 mb-2 px-3 tracking-wider">Panel Admin</p>
        <router-link to="/admin/books" class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-slate-600 hover:bg-indigo-50 hover:text-indigo-600 transition-all duration-200 font-medium" active-class="!bg-indigo-50 !text-indigo-600 !font-semibold" @click="$emit('close')">
          <Icon name="book" size="18" /> Kelola Perpustakaan
        </router-link>
      </div>
    </div>

    <div class="mt-auto px-4 pt-3 border-t border-slate-100">
      <div class="bg-slate-50 rounded-xl p-3 mb-2.5" v-if="auth.user">
        <div class="flex items-center gap-3">
          <div class="w-9 h-9 rounded-full bg-gradient-to-br from-indigo-400 to-indigo-600 flex items-center justify-center text-white text-sm font-bold shrink-0">
            {{ (auth.user.username || auth.user.name || '?')[0].toUpperCase() }}
          </div>
          <div class="min-w-0 flex-1">
            <div class="text-sm font-semibold text-slate-800 truncate">{{ auth.user.username || auth.user.name }}</div>
            <div class="flex items-center gap-1.5 mt-0.5">
              <span v-if="isAdmin" class="text-[10px] bg-indigo-100 text-indigo-700 px-2 py-0.5 rounded-full font-semibold">Admin</span>
              <span v-else-if="auth.user.membership === 'premium'" class="text-[10px] bg-amber-100 text-amber-700 px-2 py-0.5 rounded-full font-semibold">Premium</span>
              <span v-else class="text-[10px] bg-slate-200 text-slate-500 px-2 py-0.5 rounded-full font-medium">Gratis</span>
            </div>
          </div>
        </div>
      </div>
      <button @click="handleLogout" class="flex items-center gap-2.5 text-sm text-slate-500 hover:text-red-500 transition-all duration-200 w-full px-3 py-2.5 rounded-xl hover:bg-red-50 font-medium">
        <Icon name="log-out" size="16" /> Keluar
      </button>
    </div>
  </aside>
</template>