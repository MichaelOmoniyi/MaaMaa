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
          <NuxtLink 
            to="/profile"
            class="w-full flex items-center space-x-3 p-3 rounded hover:bg-[#D1E7DD] transition-colors text-left"
          >
            <UserIcon class="w-5 h-5 text-dark-green" />
            <span class="text-dark-green">My Profile</span>
          </NuxtLink>
          <button class="w-full flex items-center space-x-3 p-3 rounded bg-[#C1D7C9] transition-colors text-left">
            <CogIcon class="w-5 h-5 text-dark-green" />
            <span class="text-dark-green">Settings</span>
          </button>
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

    <!-- Main Settings Content -->
    <div class="flex-1 overflow-y-auto p-6">
      <div class="max-w-2xl mx-auto">
        <h2 class="text-3xl font-bold text-gray-900 mb-8">Settings</h2>
        
        <div class="space-y-6">
          <!-- Account Settings -->
          <div class="bg-white rounded-lg border border-gray-200 p-6">
            <h3 class="text-lg font-semibold text-gray-900 mb-4">Account Settings</h3>
            <div class="space-y-4">
              <div class="flex items-center justify-between">
                <div>
                  <p class="font-medium text-gray-900">Email Notifications</p>
                  <p class="text-sm text-gray-600">Receive email updates about your chats</p>
                </div>
                <button 
                  @click="settings.emailNotifications = !settings.emailNotifications"
                  :class="[
                    'relative inline-flex h-6 w-11 items-center rounded-full transition-colors focus:outline-none focus:ring-2 focus:ring-primary-green focus:ring-offset-2',
                    settings.emailNotifications ? 'bg-primary-green' : 'bg-gray-200'
                  ]"
                >
                  <span 
                    :class="[
                      'inline-block h-4 w-4 transform rounded-full bg-white transition-transform',
                      settings.emailNotifications ? 'translate-x-6' : 'translate-x-1'
                    ]"
                  ></span>
                </button>
              </div>
              
              <div class="flex items-center justify-between">
                <div>
                  <p class="font-medium text-gray-900">Marketing Emails</p>
                  <p class="text-sm text-gray-600">Receive tips and updates about African catering</p>
                </div>
                <button 
                  @click="settings.marketingEmails = !settings.marketingEmails"
                  :class="[
                    'relative inline-flex h-6 w-11 items-center rounded-full transition-colors focus:outline-none focus:ring-2 focus:ring-primary-green focus:ring-offset-2',
                    settings.marketingEmails ? 'bg-primary-green' : 'bg-gray-200'
                  ]"
                >
                  <span 
                    :class="[
                      'inline-block h-4 w-4 transform rounded-full bg-white transition-transform',
                      settings.marketingEmails ? 'translate-x-6' : 'translate-x-1'
                    ]"
                  ></span>
                </button>
              </div>
            </div>
          </div>

          <!-- Chat Settings -->
          <div class="bg-white rounded-lg border border-gray-200 p-6">
            <h3 class="text-lg font-semibold text-gray-900 mb-4">Chat Preferences</h3>
            <div class="space-y-4">
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Default Language</label>
                <select 
                  v-model="settings.language"
                  class="w-full border border-gray-300 rounded-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-primary-green focus:border-transparent"
                >
                  <option value="english">English</option>
                  <option value="yoruba">Yoruba</option>
                  <option value="igbo">Igbo</option>
                  <option value="hausa">Hausa</option>
                  <option value="pidgin">Pidgin English</option>
                </select>
              </div>
              
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Response Style</label>
                <select 
                  v-model="settings.responseStyle"
                  class="w-full border border-gray-300 rounded-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-primary-green focus:border-transparent"
                >
                  <option value="professional">Professional</option>
                  <option value="friendly">Friendly</option>
                  <option value="detailed">Detailed</option>
                  <option value="concise">Concise</option>
                </select>
              </div>
            </div>
          </div>

          <!-- Data & Privacy -->
          <div class="bg-white rounded-lg border border-gray-200 p-6">
            <h3 class="text-lg font-semibold text-gray-900 mb-4">Data & Privacy</h3>
            <div class="space-y-4">
              <button 
                @click="exportChatHistory"
                class="w-full text-left px-4 py-3 border border-gray-200 rounded-lg hover:bg-gray-50 transition-colors"
              >
                <p class="font-medium text-gray-900">Export Chat History</p>
                <p class="text-sm text-gray-600">Download all your conversations</p>
              </button>
              
              <button 
                @click="clearAllChats"
                class="w-full text-left px-4 py-3 border border-gray-200 rounded-lg hover:bg-gray-50 transition-colors"
              >
                <p class="font-medium text-gray-900">Clear All Chats</p>
                <p class="text-sm text-gray-600">Permanently delete all conversation history</p>
              </button>
              
              <button 
                @click="deleteAccount"
                class="w-full text-left px-4 py-3 border border-red-200 rounded-lg hover:bg-red-50 transition-colors text-red-600"
              >
                <p class="font-medium">Delete Account</p>
                <p class="text-sm">Permanently delete your account and all data</p>
              </button>
            </div>
          </div>

          <!-- Save Settings -->
          <div class="flex justify-end">
            <button 
              @click="saveSettings"
              class="btn-primary flex items-center"
              :disabled="loading"
            >
              <svg v-if="loading" class="animate-spin -ml-1 mr-2 h-4 w-4 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
              </svg>
              Save Settings
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- User Menu Dropdown -->
    <div v-if="showUserMenu" class="fixed inset-0 z-40" @click="showUserMenu = false">
      <div 
        class="absolute bottom-20 left-4 bg-white rounded-lg shadow-lg border border-gray-200 py-2 w-64"
        @click.stop
      >
        <button 
          class="w-full px-4 py-2 text-left hover:bg-gray-50 flex items-center space-x-3"
        >
          <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"></path>
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
          </svg>
          <span>Settings</span>
        </button>
        <NuxtLink 
          to="/profile"
          @click="showUserMenu = false"
          class="w-full px-4 py-2 text-left hover:bg-gray-50 flex items-center space-x-3"
        >
          <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"></path>
          </svg>
          <span>My Profile</span>
        </NuxtLink>
        <div class="border-t border-gray-200 my-2"></div>
        <button 
          @click="logout"
          class="w-full px-4 py-2 text-left hover:bg-gray-50 flex items-center space-x-3 text-red-600"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"></path>
          </svg>
          <span>Log Out</span>
        </button>
      </div>
    </div>
  </ChatLayout>
</template>

<script setup>
definePageMeta({
  middleware: ['auth'],
  layout: false
});

import { useAuthStore } from '~/store/auth';
import ChatIcon from '~/components/icons/ChatIcon.vue';
import QuestionMarkCircleIcon from '~/components/icons/QuestionMarkCircleIcon.vue';
import UserIcon from '~/components/icons/UserIcon.vue';
import CogIcon from '~/components/icons/CogIcon.vue';

const authStore = useAuthStore();

// UI State
const sidebarCollapsed = ref(false);
const showUserMenu = ref(false);
const loading = ref(false);

// Settings data
const settings = reactive({
  emailNotifications: true,
  marketingEmails: false,
  language: 'english',
  responseStyle: 'friendly'
});

// Functions
const saveSettings = async () => {
  loading.value = true;
  try {
    // Here you would typically call an API to save the settings
    await new Promise(resolve => setTimeout(resolve, 1000)); // Simulate API call
    
    // Show success message (you might want to add a toast notification)
    console.log('Settings saved successfully');
  } catch (error) {
    console.error('Error saving settings:', error);
  } finally {
    loading.value = false;
  }
};

const exportChatHistory = () => {
  // Create a dummy export
  const exportData = {
    timestamp: new Date().toISOString(),
    user: authStore.user?.name || 'User',
    chats: [
      {
        id: '1',
        title: 'Jollof Event for 150 Guests',
        date: '2024-12-15',
        messages: [
          { role: 'user', content: 'How much food should I prepare for 150 guests?' },
          { role: 'assistant', content: 'For 150 guests, I recommend planning for...' }
        ]
      }
    ]
  };
  
  const blob = new Blob([JSON.stringify(exportData, null, 2)], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `maamaa-chat-history-${Date.now()}.json`;
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
  URL.revokeObjectURL(url);
};

const clearAllChats = () => {
  if (confirm('Are you sure you want to clear all your chat history? This action cannot be undone.')) {
    // Here you would typically call an API to clear all chats
    console.log('All chats cleared');
  }
};

const deleteAccount = () => {
  if (confirm('Are you sure you want to delete your account? This action cannot be undone and will permanently delete all your data.')) {
    // Here you would typically call an API to delete the account
    console.log('Account deletion requested');
  }
};

const logout = () => {
  authStore.logout();
  showUserMenu.value = false;
  navigateTo('/');
};
</script>

<style scoped>
.btn-primary {
  background-color: #007C2E;
  color: white;
  padding: 0.75rem 1.5rem;
  border-radius: 0.5rem;
  transition: all 0.2s;
  font-weight: 500;
  border: none;
  cursor: pointer;
}

.btn-primary:hover {
  background-color: #5DBB63;
}

.btn-primary:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Custom focus styles */
.focus\:ring-2:focus {
  box-shadow: 0 0 0 2px rgba(0, 124, 46, 0.2);
}
</style>
