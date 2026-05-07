<script setup>
import { ref } from "vue"
import { useRouter } from "vue-router"
import api from "../services/api"

const router = useRouter()

const username = ref("")
const email = ref("")
const password = ref("")
const message = ref("")

const register = async () => {
  try {
    await api.post("/auth/register", {
      username: username.value,
      email: email.value,
      password: password.value,
    })

    message.value = "Register successful"

    setTimeout(() => {
      router.push("/login")
    },)
  }
  catch (err) {
    message.value = "Register failed"
  }
}
</script>

<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-100">

    <div class="bg-white p-8 rounded-xl shadow-md w-96">

      <h1 class="text-2xl font-bold mb-6 text-center">
        Register
      </h1>

      <input
        v-model="username"
        type="text"
        placeholder="Username"
        class="w-full border p-3 rounded mb-4"
      />

      <input
        v-model="email"
        type="email"
        placeholder="Email"
        class="w-full border p-3 rounded mb-4"
      />

      <input
        v-model="password"
        type="password"
        placeholder="Password"
        class="w-full border p-3 rounded mb-4"
      />

      <button
        @click="register"
        class="w-full bg-green-500 text-white p-3 rounded"
      >
        Register
      </button>

      <p class="mt-4 text-center">
        {{ message }}
      </p>

      <p class="mt-4 text-center">
        Already have account?

        <router-link
          to="/login"
          class="text-blue-500"
        >
          Login
        </router-link>
      </p>

    </div>
  </div>
</template>