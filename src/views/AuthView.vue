<script setup>
import { ref } from 'vue'
import { supabase } from '../supabase'

const loading = ref(false)
const email = ref('')

const handleLogin = async () => {
  try {
    loading.value = true
    const { error } = await supabase.auth.signInWithOtp({
      email: email.value,
    })
    if (error) throw error
    alert('Check your email for the login link!')
  } catch (error) {
    if (error instanceof Error) {
      alert(error.message)
    }
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <form class="flex justify-center items-center" @submit.prevent="handleLogin">
    <div class="w-full max-w-md p-6 bg-white rounded shadow-md">
      <h1 class="text-2xl font-bold text-center mb-4">Supabase + Vue 3</h1>
      <p class="text-center text-gray-600 mb-6">Sign in via magic link with your email below</p>
      <div class="mb-4">
        <input
          class="w-full px-4 py-2 border border-gray-300 rounded focus:outline-none focus:ring-2 focus:ring-blue-500"
          required
          type="email"
          placeholder="Your email"
          v-model="email"
        />
      </div>
      <div>
        <input
          type="submit"
          class="w-full px-4 py-2 bg-blue-500 text-white font-semibold rounded hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500"
          :value="loading ? 'Loading' : 'Send magic link'"
          :disabled="loading"
        />
      </div>
    </div>
  </form>
</template>
