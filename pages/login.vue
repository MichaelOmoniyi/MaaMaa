<template>
  <div class="min-h-screen bg-pale-green py-12">
    <div class="container mx-auto px-4">
      <div class="max-w-md mx-auto bg-white rounded-lg shadow-lg overflow-hidden">
        <div class="bg-primary-green p-4">
          <h2 class="text-2xl font-bold text-white text-center">Log In</h2>
        </div>
        <div class="p-6">
          <form @submit.prevent="login">
            <div class="mb-4">
              <label class="block text-dark-text text-sm font-bold mb-2" for="email">Email</label>
              <input
                v-model="form.email"
                class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                id="email"
                type="email"
                placeholder="john@example.com"
                required
              />
            </div>
            <div class="mb-6">
              <label class="block text-dark-text text-sm font-bold mb-2" for="password">Password</label>
              <input
                v-model="form.password"
                class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                id="password"
                type="password"
                placeholder="********"
                required
              />
            </div>
            <div class="flex items-center justify-between mb-6">
              <div class="flex items-center">
                <input
                  v-model="form.rememberMe"
                  class="mr-2 leading-tight"
                  type="checkbox"
                  id="remember"
                />
                <label class="text-sm text-gray-600" for="remember">
                  Remember me
                </label>
              </div>
              <div>
                <a href="#" class="text-sm text-primary-green hover:underline">
                  Forgot Password?
                </a>
              </div>
            </div>
            <div class="flex items-center justify-center">
              <button
                class="btn-primary w-full text-center"
                type="submit"
              >
                Log In
              </button>
            </div>
          </form>
          <div class="mt-4 text-center">
            <p class="text-sm text-gray-600">
              Don't have an account?
              <NuxtLink to="/register" class="text-primary-green hover:underline">Register</NuxtLink>
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const form = reactive({
  email: '',
  password: '',
  rememberMe: false
});

const login = () => {
  // This is just a placeholder function for now
  // In a real app, you'd validate and submit the form to your backend
  console.log('Login submitted:', form);
  // Navigate to dashboard after successful login
  navigateTo('/');
};
</script> 