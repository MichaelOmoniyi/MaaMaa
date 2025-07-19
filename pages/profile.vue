<template>
  <ChatLayout v-model:collapsed="sidebarCollapsed">
    <template #sidebar="{ sidebarCollapsed, toggleSidebar }">
      <!-- Sidebar Header -->
      <div class="p-4 border-b border-[#D1E7DD]">
        <div class="flex items-center justify-between">
          <div v-if="!sidebarCollapsed" class="flex items-center space-x-3">
            <img src="/images/maamaa-logo.png" alt="MaaMaa" class="h-8 w-auto" />
          </div>
          <button 
            @click="toggleSidebar"
            class="p-2 hover:bg-[#D1E7DD] rounded transition-colors"
          >
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"></path>
            </svg>
          </button>
        </div>
      </div>

      <!-- New Chat Button -->
      <div class="p-4">
        <NuxtLink 
          to="/chat"
          :class="[
            'w-full bg-primary-green hover:bg-light-green text-white py-2 px-4 rounded transition-colors flex items-center justify-center space-x-2',
            sidebarCollapsed ? 'px-2' : ''
          ]"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path>
          </svg>
          <span v-if="!sidebarCollapsed">New Chat</span>
        </NuxtLink>
      </div>

      <!-- Chat History -->
      <div v-if="!sidebarCollapsed" class="flex-1 overflow-y-auto px-4">
        <div class="space-y-2">
          <div class="text-xs text-gray-500 px-3 py-2">Recent Chats</div>
          <div class="p-3 rounded cursor-pointer hover:bg-[#D1E7DD] transition-colors">
            <p class="text-sm text-dark-green truncate">Jollof Event for 150 Guests</p>
            <p class="text-xs text-gray-500">Today</p>
          </div>
          <div class="p-3 rounded cursor-pointer hover:bg-[#D1E7DD] transition-colors">
            <p class="text-sm text-dark-green truncate">Coconut Rice High Class Event</p>
            <p class="text-xs text-gray-500">Yesterday</p>
          </div>
        </div>
      </div>

      <!-- Sidebar Footer -->
      <div class="border-t border-[#D1E7DD]">
        <!-- Navigation Menu -->
        <div v-if="!sidebarCollapsed" class="p-4 space-y-2">
          <NuxtLink 
            to="/chat"
            class="w-full flex items-center space-x-3 p-3 rounded hover:bg-[#D1E7DD] transition-colors text-left"
          >
            <ChatIcon class="w-5 h-5 text-dark-green" />
            <span class="text-dark-green">Chat</span>
          </NuxtLink>
          <button class="w-full flex items-center space-x-3 p-3 rounded hover:bg-[#D1E7DD] transition-colors text-left">
            <QuestionMarkCircleIcon class="w-5 h-5 text-dark-green" />
            <span class="text-dark-green">Get Help</span>
          </button>
          <button class="w-full flex items-center space-x-3 p-3 rounded bg-[#C1D7C9] transition-colors text-left">
            <UserIcon class="w-5 h-5 text-dark-green" />
            <span class="text-dark-green">My Profile</span>
          </button>
          <NuxtLink 
            to="/settings"
            class="w-full flex items-center space-x-3 p-3 rounded hover:bg-[#D1E7DD] transition-colors text-left"
          >
            <CogIcon class="w-5 h-5 text-dark-green" />
            <span class="text-dark-green">Settings</span>
          </NuxtLink>
        </div>

        <!-- User Profile -->
        <div v-if="authStore.user" class="p-4 border-t border-[#D1E7DD]">
          <div 
            @click="showUserMenu = !showUserMenu"
            :class="[
              'flex items-center space-x-3 p-3 rounded hover:bg-[#D1E7DD] transition-colors cursor-pointer',
              sidebarCollapsed ? 'justify-center' : ''
            ]"
          >
            <div class="w-8 h-8 bg-primary-green rounded-full flex items-center justify-center">
              <span class="text-white text-sm font-medium">
                {{ authStore.user.name?.charAt(0).toUpperCase() || 'U' }}
              </span>
            </div>
            <div v-if="!sidebarCollapsed" class="flex-1 min-w-0">
              <p class="text-sm text-dark-green truncate">{{ authStore.user.name || 'User' }}</p>
              <p class="text-xs text-gray-500">{{ authStore.user.plan || 'Free plan' }}</p>
            </div>
            <svg v-if="!sidebarCollapsed" class="w-4 h-4 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path>
            </svg>
          </div>
        </div>
      </div>
    </template>
        <div class="p-6">
          <div v-if="loading" class="flex justify-center py-8">
            <svg class="animate-spin h-8 w-8 text-primary-green" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
          </div>
          
          <div v-else-if="!authStore.user">
            <div class="text-center py-8">
              <p class="text-lg text-gray-600 mb-4">You need to be logged in to view your profile.</p>
              <NuxtLink to="/login" class="btn-primary">Log In</NuxtLink>
            </div>
          </div>
          
          <div v-else>
            <!-- Success/Error messages -->
            <div v-if="successMessage" class="mb-6 p-3 bg-green-100 border border-green-400 text-green-700 rounded">
              {{ successMessage }}
            </div>
            <div v-if="error" class="mb-6 p-3 bg-red-100 border border-red-400 text-red-700 rounded">
              {{ error }}
            </div>

            <!-- Profile Information -->
            <div class="mb-8">
              <h3 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">Account Information</h3>
              
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                  <label class="block text-dark-text text-sm font-bold mb-2">Email</label>
                  <div class="text-gray-700">{{ authStore.user.email }}</div>
                </div>
                <div>
                  <label class="block text-dark-text text-sm font-bold mb-2">Full Name</label>
                  <div class="text-gray-700">{{ authStore.user.user_metadata?.full_name || 'Not provided' }}</div>
                </div>
              </div>
            </div>
            
            <!-- Update Profile Form -->
            <form @submit.prevent="updateProfile" class="mb-8">
              <h3 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">Update Profile</h3>
              
              <div class="mb-4">
                <label class="block text-dark-text text-sm font-bold mb-2" for="fullName">Full Name</label>
                <input
                  v-model="form.fullName"
                  class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                  id="fullName"
                  type="text"
                  placeholder="Enter your full name"
                  :disabled="updateLoading"
                />
              </div>
              
              <div class="flex justify-end">
                <button
                  class="btn-primary flex items-center"
                  type="submit"
                  :disabled="updateLoading"
                >
                  <svg v-if="updateLoading" class="animate-spin -ml-1 mr-2 h-4 w-4 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                  </svg>
                  Update Profile
                </button>
              </div>
            </form>
            
            <!-- Change Password Form -->
            <form @submit.prevent="changePassword" class="mb-8">
              <h3 class="text-xl font-semibold text-gray-800 mb-4 border-b pb-2">Change Password</h3>
              
              <div class="mb-4">
                <label class="block text-dark-text text-sm font-bold mb-2" for="newPassword">New Password</label>
                <input
                  v-model="passwordForm.newPassword"
                  class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                  id="newPassword"
                  type="password"
                  placeholder="Enter your new password"
                  :disabled="passwordLoading"
                />
              </div>
              
              <div class="mb-4">
                <label class="block text-dark-text text-sm font-bold mb-2" for="confirmNewPassword">Confirm New Password</label>
                <input
                  v-model="passwordForm.confirmNewPassword"
                  class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                  id="confirmNewPassword"
                  type="password"
                  placeholder="Confirm your new password"
                  :disabled="passwordLoading"
                />
              </div>
              
              <div class="flex justify-end">
                <button
                  class="btn-primary flex items-center"
                  type="submit"
                  :disabled="passwordLoading"
                >
                  <svg v-if="passwordLoading" class="animate-spin -ml-1 mr-2 h-4 w-4 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                  </svg>
                  Change Password
                </button>
              </div>
            </form>
          </div>
        </div>
  </ChatLayout>
</template>

<script setup>
import { useAuthStore } from '~/store/auth';

const authStore = useAuthStore();
const { $supabase } = useNuxtApp();

const loading = ref(true);
const updateLoading = ref(false);
const passwordLoading = ref(false);
const successMessage = ref('');
const error = ref('');

const form = reactive({
  fullName: '',
});

const passwordForm = reactive({
  newPassword: '',
  confirmNewPassword: '',
});

// Fetch user data on mount
onMounted(async () => {
  try {
    if (authStore.user) {
      form.fullName = authStore.user.user_metadata?.full_name || '';
    }
  } catch (err) {
    error.value = err.message || 'Failed to load profile data';
  } finally {
    loading.value = false;
  }
});

// Update user profile
const updateProfile = async () => {
  updateLoading.value = true;
  error.value = '';
  successMessage.value = '';
  
  try {
    const { error: updateError } = await $supabase.auth.updateUser({
      data: { full_name: form.fullName }
    });

    if (updateError) throw updateError;
    
    successMessage.value = 'Profile updated successfully!';
  } catch (err) {
    error.value = err.message || 'Failed to update profile';
  } finally {
    updateLoading.value = false;
  }
};

// Change password
const changePassword = async () => {
  passwordLoading.value = true;
  error.value = '';
  successMessage.value = '';
  
  try {
    // Check if passwords match
    if (passwordForm.newPassword !== passwordForm.confirmNewPassword) {
      throw new Error('Passwords do not match');
    }
    
    // Update password
    const { error: passwordError } = await $supabase.auth.updateUser({
      password: passwordForm.newPassword
    });

    if (passwordError) throw passwordError;
    
    // Clear form
    passwordForm.newPassword = '';
    passwordForm.confirmNewPassword = '';
    
    successMessage.value = 'Password changed successfully!';
  } catch (err) {
    error.value = err.message || 'Failed to change password';
  } finally {
    passwordLoading.value = false;
  }
};

// Clear messages on form changes
watch(() => form.fullName, () => {
  error.value = '';
  successMessage.value = '';
});

watch(() => [passwordForm.newPassword, passwordForm.confirmNewPassword], () => {
  error.value = '';
  successMessage.value = '';
});
</script>
