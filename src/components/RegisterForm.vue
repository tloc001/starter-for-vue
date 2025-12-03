<script setup>
import { ref } from 'vue'
import { account } from '@/lib/appwrite'
import { ID } from 'appwrite'

const email = ref('')
const password = ref('')
const name = ref('')
const status = ref('idle') // idle, loading, success, error
const errorMessage = ref('')
const successMessage = ref('')

const handleRegister = async () => {
  if (status.value === 'loading') return
  
  // Validate form
  if (!email.value || !password.value) {
    errorMessage.value = 'Vui lòng nhập email và mật khẩu'
    status.value = 'error'
    return
  }
  
  if (password.value.length < 8) {
    errorMessage.value = 'Mật khẩu phải có ít nhất 8 ký tự'
    status.value = 'error'
    return
  }
  
  status.value = 'loading'
  errorMessage.value = ''
  successMessage.value = ''
  
  try {
    const result = await account.create(
      ID.unique(), // Generate unique user ID
      email.value,
      password.value,
      name.value || undefined
    )
    
    console.log('User created:', result)
    status.value = 'success'
    successMessage.value = 'Đăng ký thành công!'
    
    // Reset form
    email.value = ''
    password.value = ''
    name.value = ''
    
  } catch (error) {
    console.error('Registration error:', error)
    status.value = 'error'
    errorMessage.value = error.message || 'Đã xảy ra lỗi khi đăng ký'
  }
}
</script>

<template>
  <div class="flex min-h-screen items-center justify-center bg-gray-50 px-4 py-12 sm:px-6 lg:px-8">
    <div class="w-full max-w-md space-y-8">
      <div>
        <h2 class="mt-6 text-center text-3xl font-bold tracking-tight text-gray-900">
          Đăng ký tài khoản
        </h2>
        <p class="mt-2 text-center text-sm text-gray-600">
          Tạo tài khoản mới với Appwrite
        </p>
      </div>
      
      <form class="mt-8 space-y-6" @submit.prevent="handleRegister">
        <div class="-space-y-px rounded-md shadow-sm">
          <div>
            <label for="name" class="sr-only">Tên</label>
            <input
              id="name"
              v-model="name"
              type="text"
              autocomplete="name"
              class="relative block w-full rounded-t-md border-0 px-3 py-2 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:z-10 focus:ring-2 focus:ring-inset focus:ring-[#FD366E] sm:text-sm sm:leading-6"
              placeholder="Tên (tùy chọn)"
            />
          </div>
          <div>
            <label for="email" class="sr-only">Email</label>
            <input
              id="email"
              v-model="email"
              type="email"
              autocomplete="email"
              required
              class="relative block w-full border-0 px-3 py-2 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:z-10 focus:ring-2 focus:ring-inset focus:ring-[#FD366E] sm:text-sm sm:leading-6"
              placeholder="Email"
            />
          </div>
          <div>
            <label for="password" class="sr-only">Mật khẩu</label>
            <input
              id="password"
              v-model="password"
              type="password"
              autocomplete="new-password"
              required
              class="relative block w-full rounded-b-md border-0 px-3 py-2 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:z-10 focus:ring-2 focus:ring-inset focus:ring-[#FD366E] sm:text-sm sm:leading-6"
              placeholder="Mật khẩu (tối thiểu 8 ký tự)"
            />
          </div>
        </div>

        <!-- Success Message -->
        <div
          v-if="status === 'success'"
          class="rounded-md bg-green-50 p-4"
        >
          <div class="flex">
            <div class="flex-shrink-0">
              <svg class="h-5 w-5 text-green-400" viewBox="0 0 20 20" fill="currentColor">
                <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
              </svg>
            </div>
            <div class="ml-3">
              <p class="text-sm font-medium text-green-800">
                {{ successMessage }}
              </p>
            </div>
          </div>
        </div>

        <!-- Error Message -->
        <div
          v-if="status === 'error'"
          class="rounded-md bg-red-50 p-4"
        >
          <div class="flex">
            <div class="flex-shrink-0">
              <svg class="h-5 w-5 text-red-400" viewBox="0 0 20 20" fill="currentColor">
                <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clip-rule="evenodd" />
              </svg>
            </div>
            <div class="ml-3">
              <p class="text-sm font-medium text-red-800">
                {{ errorMessage }}
              </p>
            </div>
          </div>
        </div>

        <div>
          <button
            type="submit"
            :disabled="status === 'loading'"
            class="group relative flex w-full justify-center rounded-md bg-[#FD366E] px-3 py-2 text-sm font-semibold text-white hover:bg-[#E62E5C] focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-[#FD366E] disabled:opacity-50 disabled:cursor-not-allowed"
          >
            <span v-if="status === 'loading'" class="flex items-center">
              <svg
                class="mr-2 h-4 w-4 animate-spin"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
              >
                <circle
                  class="opacity-25"
                  cx="12"
                  cy="12"
                  r="10"
                  stroke="currentColor"
                  stroke-width="4"
                ></circle>
                <path
                  class="opacity-75"
                  fill="currentColor"
                  d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
                ></path>
              </svg>
              Đang xử lý...
            </span>
            <span v-else>Đăng ký</span>
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
/* Additional custom styles if needed */
</style>
