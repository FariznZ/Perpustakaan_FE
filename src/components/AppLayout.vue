<script setup>
import { ref } from 'vue'
import AppSidebar from './AppSidebar.vue'
import ChatBox from './ChatBox.vue'
import Icon from './Icon.vue'

const sidebarOpen = ref(false)

const toggleSidebar = () => sidebarOpen.value = !sidebarOpen.value
const closeSidebar = () => sidebarOpen.value = false
</script>

<template>
  <div class="flex h-screen overflow-hidden bg-slate-50">
    <div
      v-if="sidebarOpen"
      class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm z-30 lg:hidden"
      @click="closeSidebar"
    ></div>

    <AppSidebar :open="sidebarOpen" @close="closeSidebar" />

    <main class="flex-1 lg:ml-64 overflow-y-auto min-h-screen">
      <div class="sticky top-0 z-20 bg-white/70 backdrop-blur-lg border-b border-slate-200/80 lg:hidden">
        <div class="flex items-center justify-between px-4 py-3">
          <button
            @click="toggleSidebar"
            class="p-2 -ml-2 text-slate-500 hover:text-slate-800 rounded-lg hover:bg-slate-100 transition"
            aria-label="Toggle sidebar"
          >
            <Icon :name="sidebarOpen ? 'x' : 'menu'" size="22" />
          </button>
          <div class="flex items-center gap-2">
            <div class="w-7 h-7 bg-gradient-to-br from-indigo-500 to-indigo-600 rounded-lg flex items-center justify-center">
              <svg class="w-4 h-4 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                <path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"/>
                <path d="M12 2v20"/>
              </svg>
            </div>
            <span class="text-sm font-bold text-slate-800">EduLibrary</span>
          </div>
          <div class="w-9"></div>
        </div>
      </div>
      <div class="p-4 md:p-8 lg:p-10 max-w-6xl mx-auto min-h-full">
        <router-view v-slot="{ Component }">
          <transition name="page" mode="out-in">
            <component :is="Component" />
          </transition>
        </router-view>
      </div>
    </main>
    <ChatBox />
  </div>
</template>