<template>
  <div class="min-h-screen bg-pale-green py-12">
    <div class="container mx-auto px-4">
      <div class="max-w-md mx-auto bg-white rounded-lg shadow-lg overflow-hidden">
        <div class="bg-primary-green p-4">
          <h2 class="text-2xl font-bold text-white text-center">Create an Account</h2>
        </div>
        <div class="p-6">
          <form @submit.prevent="register">
            <div class="mb-4">
              <label class="block text-dark-text text-sm font-bold mb-2" for="name">Full Name</label>
              <input
                v-model="form.name"
                class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                id="name"
                type="text"
                placeholder="John Doe"
                required
              />
            </div>
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
            <div class="mb-4">
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
            <div class="mb-6">
              <label class="block text-dark-text text-sm font-bold mb-2" for="confirmPassword">Confirm Password</label>
              <input
                v-model="form.confirmPassword"
                class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                id="confirmPassword"
                type="password"
                placeholder="********"
                required
              />
            </div>
            <div class="flex items-center justify-center">
              <button
                class="btn-primary w-full text-center"
                type="submit"
              >
                Register
              </button>
            </div>
          </form>
          <div class="mt-4 text-center">
            <p class="text-sm text-gray-600">
              Already have an account?
              <NuxtLink to="/login" class="text-primary-green hover:underline">Log In</NuxtLink>
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const form = reactive({
  name: '',
  email: '',
  password: '',
  confirmPassword: ''
});

const register = () => {
  // This is just a placeholder function for now
  // In a real app, you'd validate and submit the form to your backend
  if (form.password !== form.confirmPassword) {
    alert('Passwords do not match');
    return;
  }
  
  console.log('Registration submitted:', form);
  // Navigate to login or dashboard after successful registration
  navigateTo('/login');
};
</script> 