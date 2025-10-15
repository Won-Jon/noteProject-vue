<!-- src/views/LoginView.vue -->
<template>
  <div style="max-width: 400px; margin: 60px auto; padding: 20px">
    <h2>登录</h2>
    <form @submit.prevent="login">
      <div>
        <input
          v-model="username"
          placeholder="用户名"
          required
          style="width: 100%; padding: 8px; margin: 8px 0"
        />
      </div>
      <div>
        <input
          v-model="password"
          type="password"
          placeholder="密码"
          required
          style="width: 100%; padding: 8px; margin: 8px 0"
        />
      </div>
      <button
        type="submit"
        :disabled="loading"
        style="
          width: 100%;
          padding: 10px;
          background: #1890ff;
          color: white;
          border: none;
          border-radius: 4px;
        "
      >
        {{ loading ? '登录中...' : '登录' }}
      </button>
      <p style="text-align: center; margin-top: 16px">
        没有账号？<router-link to="/register">注册</router-link>
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

const login = async () => {
  loading.value = true
  error.value = ''
  try {
    const res = await axios.post('http://localhost:8080/api/auth/login', {
      username: username.value,
      password: password.value,
    })
    localStorage.setItem('token', res.data.token)
    // 设置全局 Authorization header
    axios.defaults.headers.common['Authorization'] = `Bearer ${res.data.token}`
    router.push('/')
  } catch (err: unknown) {
    if (axios.isAxiosError(err)) {
      const axiosError = err as import('axios').AxiosError
      const msg =
        axiosError.response?.data &&
        typeof axiosError.response.data === 'object' &&
        'message' in axiosError.response.data
          ? (axiosError.response.data as { message?: string }).message
          : undefined
      error.value = msg || '登录失败'
    } else {
      error.value = '未知错误'
    }
  } finally {
    loading.value = false
  }
}
</script>
