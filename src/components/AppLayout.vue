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
    <!-- Backdrop mobile -->
    <div
      v-if="sidebarOpen"
      class="fixed inset-0 bg-slate-900/50 z-30 lg:hidden"
      @click="closeSidebar"
    ></div>

    <AppSidebar :open="sidebarOpen" @close="closeSidebar" />

    <main class="flex-1 lg:ml-64 overflow-y-auto min-h-screen">
      <div class="sticky top-0 z-20 bg-slate-50/80 backdrop-blur border-b border-slate-200 lg:hidden">
        <button
          @click="toggleSidebar"
          class="p-3 text-slate-600 hover:text-slate-900"
          aria-label="Toggle sidebar"
        >
          <Icon :name="sidebarOpen ? 'x' : 'menu'" size="24" />
        </button>
      </div>
      <div class="p-4 md:p-8 max-w-6xl mx-auto min-h-full">
        <router-view v-slot="{ Component }">
          <transition name="fade" mode="out-in">
            <component :is="Component" />
          </transition>
        </router-view>
      </div>
    </main>
    <ChatBox />
  </div>
</template>

<style scoped>
.fade-enter-active, .fade-leave-active { transition: opacity 0.2s ease; }
.fade-enter-from, .fade-leave-to { opacity: 0; }
</style>