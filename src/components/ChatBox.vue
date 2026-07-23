<script setup>
import { ref } from 'vue'
import { API_URL } from '../config'
import Icon from './Icon.vue'

const open = ref(false)
const messages = ref([
  { role: 'ai', text: 'Halo! Ada yang bisa saya bantu tentang perpustakaan?' }
])
const input = ref('')
const loading = ref(false)

const toggle = () => { open.value = !open.value }

const send = async () => {
  const msg = input.value.trim()
  if (!msg || loading.value) return

  messages.value.push({ role: 'user', text: msg })
  input.value = ''
  loading.value = true

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
    messages.value.push({ role: 'ai', text: e.message.includes('429') ? 'Terlalu banyak request. Tunggu beberapa saat ya.' : e.message })
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <!-- Tombol floating -->
  <button
    @click="toggle"
    class="fixed bottom-6 right-6 z-50 w-14 h-14 bg-teal-600 hover:bg-teal-700 text-white rounded-full shadow-lg flex items-center justify-center transition"
  >
    <Icon :name="open ? 'x' : 'message-circle'" size="24" />
  </button>

  <!-- Chatbox -->
  <div
    v-if="open"
    class="fixed bottom-24 right-6 z-50 w-80 sm:w-96 bg-white rounded-2xl shadow-2xl border border-slate-200 flex flex-col overflow-hidden max-h-[500px]"
  >
    <div class="bg-teal-600 text-white px-4 py-3 font-bold flex items-center gap-2">
      <Icon name="message-circle" size="18" />
      AI Assistant
    </div>

    <div class="flex-1 overflow-y-auto p-4 space-y-3 min-h-[300px] max-h-[350px]">
      <div v-for="(m, i) in messages" :key="i" :class="m.role === 'user' ? 'text-right' : ''">
        <span
          :class="m.role === 'user'
            ? 'bg-teal-600 text-white rounded-2xl rounded-br-md'
            : 'bg-slate-100 text-slate-800 rounded-2xl rounded-bl-md'"
          class="inline-block px-4 py-2 text-sm max-w-[85%] whitespace-pre-wrap"
        >{{ m.text }}</span>
      </div>
      <div v-if="loading" class="text-left">
        <span class="inline-block bg-slate-100 text-slate-500 px-4 py-2 rounded-2xl text-sm animate-pulse">Mengetik...</span>
      </div>
    </div>

    <div class="border-t border-slate-200 p-3 flex gap-2">
      <input
        v-model="input"
        @keydown.enter="send"
        type="text"
        placeholder="Tanya sesuatu..."
        class="flex-1 border border-slate-300 rounded-xl px-3 py-2 text-sm outline-none focus:border-teal-500"
      />
      <button
        @click="send"
        :disabled="loading || !input.trim()"
        class="bg-teal-600 text-white px-3 py-2 rounded-xl text-sm font-bold hover:bg-teal-700 disabled:opacity-50"
      >
        Kirim
      </button>
    </div>
  </div>
</template>