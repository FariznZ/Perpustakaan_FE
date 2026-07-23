<script setup>
import { ref, watch, nextTick } from 'vue'
import { API_URL } from '../config'
import Icon from './Icon.vue'

const open = ref(false)
const messages = ref([
  { role: 'ai', text: 'Halo! Aku **AI Teman Baca** &#x1F4DA;\n\nAda yang ingin kamu tanyakan tentang perpustakaan, buku, atau rekomendasi bacaan? Tanya aja ya!' }
])
const input = ref('')
const loading = ref(false)
const chatBody = ref(null)

const toggle = () => { open.value = !open.value }

watch(open, (v) => {
  if (v) nextTick(() => scrollToBottom())
})

const scrollToBottom = () => {
  nextTick(() => {
    if (chatBody.value) chatBody.value.scrollTop = chatBody.value.scrollHeight
  })
}

const send = async () => {
  const msg = input.value.trim()
  if (!msg || loading.value) return

  messages.value.push({ role: 'user', text: msg })
  input.value = ''
  loading.value = true
  scrollToBottom()

  try {
    const res = await fetch(`${API_URL}/ai/chat`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ message: msg })
    })
    const data = await res.json()
    if (!res.ok) {
      throw new Error(data.message || `Error ${res.status}`)
    }
    messages.value.push({ role: 'ai', text: data.message || 'Maaf, terjadi kesalahan.' })
  } catch (e) {
    const errMsg = e.message.includes('429')
      ? 'Maaf, aku kebanyakan ngobrol. Coba lagi beberapa saat ya \u{1F622}'
      : e.message.includes('FETCH') || e.message.includes('Failed to fetch')
        ? 'Aku gagal nyambung ke server. Coba lagi nanti \u{1F4E1}'
        : e.message
    messages.value.push({ role: 'ai', text: errMsg })
  } finally {
    loading.value = false
    scrollToBottom()
  }
}

const renderText = (text) => {
  return text
    .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
    .replace(/\n/g, '<br>')
}
</script>

<template>
  <!-- Tombol floating -->
  <button
    @click="toggle"
    class="fixed bottom-5 right-5 z-50 w-14 h-14 bg-gradient-to-br from-teal-500 to-emerald-600 hover:from-teal-600 hover:to-emerald-700 text-white rounded-full shadow-lg shadow-teal-500/30 flex items-center justify-center transition-all hover:scale-105 active:scale-95"
  >
    <Icon :name="open ? 'x' : 'message-circle'" size="22" />
  </button>

  <!-- Chatbox -->
  <div
    v-if="open"
    class="fixed bottom-20 right-5 z-50 w-80 sm:w-96 bg-white rounded-2xl shadow-2xl border border-slate-200 flex flex-col overflow-hidden animate-slide-up"
  >
    <!-- Header -->
    <div class="bg-gradient-to-r from-teal-600 to-emerald-600 text-white px-4 py-3.5 flex items-center gap-3">
      <div class="w-9 h-9 bg-white/20 rounded-full flex items-center justify-center">
        <svg class="w-5 h-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"/>
          <path d="M12 2v20"/>
        </svg>
      </div>
      <div>
        <div class="font-bold text-sm leading-tight">AI Teman Baca</div>
        <div class="text-[10px] text-teal-100">Asisten Perpustakaan Digital</div>
      </div>
      <button @click="open = false" class="ml-auto text-white/70 hover:text-white transition">
        <Icon name="x" size="18" />
      </button>
    </div>

    <!-- Chat body -->
    <div ref="chatBody" class="flex-1 overflow-y-auto p-4 space-y-3.5 min-h-[320px] max-h-[380px] bg-slate-50/50">
      <div v-for="(m, i) in messages" :key="i" :class="m.role === 'user' ? 'flex justify-end' : 'flex justify-start'" class="animate-fade-in">
        <div
          :class="m.role === 'user'
            ? 'bg-gradient-to-br from-teal-500 to-emerald-600 text-white rounded-2xl rounded-br-sm shadow-sm'
            : 'bg-white text-slate-700 rounded-2xl rounded-bl-sm border border-slate-200 shadow-sm'"
          class="inline-block px-4 py-2.5 text-sm max-w-[88%] leading-relaxed"
          v-html="renderText(m.text)"
        ></div>
      </div>
      <div v-if="loading" class="flex justify-start">
        <div class="bg-white text-slate-500 rounded-2xl rounded-bl-sm border border-slate-200 px-4 py-2.5 text-sm shadow-sm flex items-center gap-1">
          <span class="w-1.5 h-1.5 bg-teal-500 rounded-full animate-bounce" style="animation-delay:0ms"></span>
          <span class="w-1.5 h-1.5 bg-teal-500 rounded-full animate-bounce" style="animation-delay:150ms"></span>
          <span class="w-1.5 h-1.5 bg-teal-500 rounded-full animate-bounce" style="animation-delay:300ms"></span>
        </div>
      </div>
    </div>

    <!-- Input -->
    <div class="border-t border-slate-200 p-3 bg-white flex gap-2 items-center">
      <input
        v-model="input"
        @keydown.enter="send"
        type="text"
        placeholder="Tanya tentang buku..."
        class="flex-1 border border-slate-300 rounded-xl px-3.5 py-2.5 text-sm outline-none transition focus:border-teal-400 focus:ring-2 focus:ring-teal-500/20 placeholder:text-slate-400"
      />
      <button
        @click="send"
        :disabled="loading || !input.trim()"
        class="bg-gradient-to-r from-teal-500 to-emerald-600 text-white w-10 h-10 rounded-xl flex items-center justify-center transition disabled:opacity-40 hover:shadow-md hover:shadow-teal-500/20 active:scale-95"
      >
        <svg class="w-4.5 h-4.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
          <line x1="22" y1="2" x2="11" y2="13"/>
          <polygon points="22 2 15 22 11 13 2 9 22 2"/>
        </svg>
      </button>
    </div>
  </div>
</template>

<style scoped>
@keyframes slide-up {
  from { opacity: 0; transform: translateY(16px) scale(0.96); }
  to { opacity: 1; transform: translateY(0) scale(1); }
}
@keyframes fade-in {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}
.animate-slide-up {
  animation: slide-up 0.25s ease-out;
}
.animate-fade-in {
  animation: fade-in 0.25s ease-out;
}
</style>