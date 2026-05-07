<script setup>
import { onMounted, ref, computed } from "vue"
import { useRouter } from "vue-router"
import api from "../services/api"

const router = useRouter()
const BASE_URL = "https://shorturl-backend-4h62.onrender.com"
const originalUrl = ref("")
const urls = ref([])
const successMessage = ref("")

const isLoggedIn = computed(() => {
  return !!localStorage.getItem("token")
})

const copyToClipboard = async (text) => {
  try {
    await navigator.clipboard.writeText(text)
    alert("Copied to clipboard!")
  } catch (err) {
    console.error("Copy failed:", err)
    alert("Failed to copy")
  }
}

const createUrl = async () => {
  //
  // REQUIRE LOGIN
  //
  if (!isLoggedIn.value) {
    router.push("/login")
    return
  }

  try {
    await api.post("/url/create", {
      originalUrl: originalUrl.value,
    })

    originalUrl.value = ""

    successMessage.value = "Short URL created successfully"

    loadUrls()

    setTimeout(() => {
      successMessage.value = ""
    }, 3000)
  }
  catch (err) {
    console.log(err)
  }
}

const loadUrls = async () => {
  //
  // GUEST USERS DON'T LOAD PRIVATE URLS
  //
  if (!isLoggedIn.value) {
    return
  }

  try {
    const response = await api.get("/url/myurls")

    urls.value = response.data
  }
  catch (err) {
    console.log(err)
  }
}

const logout = () => {
  localStorage.removeItem("token")

  window.location.href = "/"
}

onMounted(() => {
  loadUrls()
})
</script>

<template>
  <div class="min-h-screen bg-gray-100">

    <!-- NAVBAR -->
    <div class="bg-white shadow p-4">

      <div class="max-w-5xl mx-auto flex justify-between items-center">

        <h1 class="text-2xl font-bold">
          ShortURL
        </h1>

        <div v-if="isLoggedIn">

          <button
            @click="logout"
            class="bg-red-500 text-white px-4 py-2 rounded"
          >
            Logout
          </button>

        </div>

        <div v-else class="flex gap-4">

          <router-link
            to="/login"
            class="text-blue-500"
          >
            Login
          </router-link>

          <router-link
            to="/register"
            class="text-green-500"
          >
            Register
          </router-link>

        </div>

      </div>

    </div>

    <!-- MAIN -->
    <div class="p-10">

      <div class="max-w-3xl mx-auto">

        <div class="text-center mb-10">

          <h1 class="text-5xl font-bold mb-4">
            Shorten Your URLs
          </h1>

          <p class="text-gray-600">
            Fast, simple, and secure URL shortener
          </p>

        </div>

        <!-- SHORTENER -->
        <div class="bg-white p-6 rounded-xl shadow mb-8">

          <input
            v-model="originalUrl"
            type="text"
            placeholder="Enter your URL"
            class="w-full border p-3 rounded mb-4"
          />

          <button
            @click="createUrl"
            class="w-full bg-blue-500 text-white p-3 rounded"
          >
            Shorten URL
          </button>

          <p
            v-if="!isLoggedIn"
            class="text-center text-gray-500 mt-4"
          >
            Login required to create URLs
          </p>

        </div>

        <!-- SUCCESS MESSAGE -->
        <p
          v-if="successMessage"
          class="bg-green-100 text-green-700 p-3 rounded mb-4"
        >
          {{ successMessage }}
        </p>

        <!-- USER URLS -->
        <div
          v-if="isLoggedIn"
          class="bg-white p-6 rounded-xl shadow"
        >

          <h2 class="text-2xl font-bold mb-4">
            My URLs
          </h2>

          <div
            v-for="url in urls"
            :key="url.id"
            class="border-b py-4"
          >

            <p class="break-all">
              <strong>Original:</strong>
              {{ url.originalUrl }}
            </p>

            <p class="break-all mt-2">
              <strong>Short URL:</strong>

              <a
                :href="`${BASE_URL}/api/url/redirect/${url.shortCode}`"
                target="_blank"
                class="text-blue-500 underline"
              >
                {{ BASE_URL }}/api/url/redirect/{{ url.shortCode }}
              </a>
            </p>

            <button
              @click="copyToClipboard(`${BASE_URL}/api/url/redirect/${url.shortCode}`)"
              class="bg-gray-200 px-3 py-1 rounded mt-2"
            >
              Copy
            </button>

            <p class="mt-2">
              <strong>Clicks:</strong>
              {{ url.clickCount }}
            </p>

            <p class="mt-2">
              <strong>Expire:</strong>

              {{
                new Date(url.expireAt)
                  .toLocaleString()
              }}
            </p>

          </div>

        </div>

      </div>

    </div>

  </div>
</template>