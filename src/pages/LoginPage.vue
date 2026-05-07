<script setup>
import { ref } from "vue"
import { useRouter } from "vue-router"
import api from "../services/api"

const router = useRouter()

const username = ref("")
const password = ref("")
const error = ref("")

const login = async () => {
  try {
    const response = await api.post("/auth/login", {
      username: username.value,
      password: password.value,
    })

    localStorage.setItem("token", response.data.token)

    router.push("/")
  }
  catch (err) {
        error.value = err.response?.data || "Login failed"
        console.log(err.response)
    }
}
</script>

<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-100">
    <div class="bg-white p-8 rounded-xl shadow-md w-96">

      <h1 class="text-2xl font-bold mb-6 text-center">
        Login
      </h1>

      <input
        v-model="username"
        type="text"
        placeholder="Username"
        class="w-full border p-3 rounded mb-4"
      />

      <input
        v-model="password"
        type="password"
        placeholder="Password"
        class="w-full border p-3 rounded mb-4"
      />

      <button
        @click="login"
        class="w-full bg-blue-500 text-white p-3 rounded"
      >
        Login
      </button>

      <p class="text-red-500 mt-4">
        {{ error }}
      </p>

      <p class="mt-4 text-center">
        No account?

        <router-link
          to="/register"
          class="text-blue-500"
        >
          Register
        </router-link>
      </p>

    </div>
  </div>
</template>