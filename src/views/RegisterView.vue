<!-- src/views/RegisterView.vue -->
<template>
  <div style="max-width: 400px; margin: 60px auto; padding: 20px">
    <h2>注册</h2>
    <form @submit.prevent="register">
      <div>
        <input
          v-model="username"
          placeholder="用户名（至少3位）"
          required
          minlength="3"
          style="width: 100%; padding: 8px; margin: 8px 0"
        />
      </div>
      <div>
        <input
          v-model="password"
          type="password"
          placeholder="密码（至少6位）"
          required
          minlength="6"
          style="width: 100%; padding: 8px; margin: 8px 0"
        />
      </div>
      <button
        type="submit"
        :disabled="loading"
        style="
          width: 100%;
          padding: 10px;
          background: #52c41a;
          color: white;
          border: none;
          border-radius: 4px;
        "
      >
        {{ loading ? '注册中...' : '注册' }}
      </button>
      <p style="text-align: center; margin-top: 16px">
        已有账号？<router-link to="/login">登录</router-link>
      </p>
      <p v-if="error" style="color: red; text-align: center">{{ error }}</p>
    </form>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

const username = ref('')
const password = ref('')
const loading = ref(false)
const error = ref('')
const router = useRouter()

const register = async () => {
  loading.value = true
  error.value = ''
  try {
    await axios.post('http://localhost:8080/api/auth/register', {
      username: username.value,
      password: password.value,
    })
    alert('注册成功！请登录')
    router.push('/login')
  } catch (err: unknown) {
    if (axios.isAxiosError(err)) {
      const axiosError = err as import('axios').AxiosError
      const msg =
        axiosError.response?.data &&
        typeof axiosError.response.data === 'object' &&
        'message' in axiosError.response.data
          ? (axiosError.response.data as { message?: string }).message
          : undefined
      error.value = msg || '注册失败'
    } else {
      error.value = '未知错误'
    }
  } finally {
    loading.value = false
  }
}
</script>
