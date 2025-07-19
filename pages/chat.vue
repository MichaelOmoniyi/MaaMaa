<template>
  <ChatLayout v-model:collapsed="sidebarCollapsed">
    <template #sidebar="{ sidebarCollapsed, toggleSidebar }">
      <!-- Sidebar Header with Toggle -->
      <div class="p-4 border-b border-[#D1E7DD]">
        <div class="flex items-center justify-between">
          <div v-if="!sidebarCollapsed" class="flex items-center space-x-3">
            <span class="text-lg font-semibold text-gray-800">Chats</span>
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
        <button 
          @click="startNewChat"
          :class="[
            'w-full bg-green-600 hover:bg-green-700 text-white py-2 px-4 rounded-lg transition-colors flex items-center justify-center space-x-2',
            sidebarCollapsed ? 'px-2' : ''
          ]"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path>
          </svg>
          <span v-if="!sidebarCollapsed">New Chat</span>
        </button>
      </div>

      <!-- Recent Chats -->
      <div v-if="!sidebarCollapsed" class="flex-1 overflow-y-auto px-4">
        <div class="mb-4">
          <h3 class="text-sm font-medium text-gray-500 mb-2">Recent chats</h3>
          <div class="space-y-2">
            <div 
              v-for="(chat, index) in chatHistory" 
              :key="index"
              @click="loadChat(chat)"
              :class="[
                'group p-3 rounded cursor-pointer hover:bg-[#D1E7DD] transition-colors',
                currentChatId === chat.id ? 'bg-[#D1E7DD]' : ''
              ]"
            >
              <div class="flex justify-between items-start">
                <div class="flex-1 min-w-0">
                  <p class="text-sm text-gray-800 truncate">{{ chat.title }}</p>
                  <p class="text-xs text-gray-500">{{ formatDate(chat.updatedAt) }}</p>
                </div>
                <button 
                  @click.stop="deleteChat(chat.id)"
                  class="p-1 hover:bg-[#C1D7C9] rounded opacity-0 group-hover:opacity-100 transition-opacity"
                >
                  <svg class="w-4 h-4 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path>
                  </svg>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Sidebar Navigation -->
      <div class="border-t border-[#D1E7DD]">
        <!-- Navigation Menu -->
        <div v-if="!sidebarCollapsed" class="p-4 space-y-2">
          <button 
            v-for="tab in navigationTabs" 
            :key="tab.key"
            @click="activeTab = tab.key"
            :class="[
              'w-full flex items-center space-x-3 p-3 rounded hover:bg-[#D1E7DD] transition-colors text-left',
              activeTab === tab.key ? 'bg-[#C1D7C9]' : ''
            ]"
          >
            <component :is="tab.icon" class="w-5 h-5 text-gray-700" />
            <span class="text-gray-700">{{ tab.label }}</span>
          </button>
        </div>

        <!-- User Profile Section -->
        <div v-if="authStore.user" class="p-4 border-t border-[#D1E7DD]">
          <div 
            @click="showUserMenu = !showUserMenu"
            :class="[
              'flex items-center space-x-3 p-3 rounded hover:bg-[#D1E7DD] transition-colors cursor-pointer relative',
              sidebarCollapsed ? 'justify-center' : ''
            ]"
          >
            <div class="w-8 h-8 bg-green-600 rounded-full flex items-center justify-center">
              <span class="text-white text-sm font-medium">
                {{ authStore.user.name?.charAt(0).toUpperCase() || 'U' }}
              </span>
            </div>
            <div v-if="!sidebarCollapsed" class="flex-1 min-w-0">
              <p class="text-sm text-gray-800 truncate">{{ authStore.user.name || 'User' }}</p>
              <p class="text-xs text-gray-500">{{ authStore.user.plan || 'Free plan' }} • <span class="text-green-600">Upgrade</span></p>
            </div>
            <svg v-if="!sidebarCollapsed" class="w-4 h-4 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path>
            </svg>
          </div>
        </div>
      </div>
    </template>

    <!-- Main Chat Area -->
    <div class="flex-1 flex flex-col bg-white">
      <!-- Chat Messages Area -->
      <div 
        class="flex-1 overflow-y-auto p-4"
        ref="chatContainer"
        v-if="activeTab === 'chat'"
      >
      >
        <!-- Login Required State -->
        <div v-if="!authStore.user" class="flex justify-center items-center h-full">
          <div class="text-center max-w-md">
            <img src="/images/maamaa-logo.png" alt="MaaMaa Logo" class="h-16 mx-auto mb-6" />
            <h3 class="text-2xl font-semibold text-gray-800 mb-4">Welcome to MaaMaa Chat</h3>
            <p class="text-gray-600 mb-6">Your AI-powered catering assistant for African cuisine</p>
            <p class="text-lg text-gray-600 mb-6">You need to be logged in to start chatting.</p>
            <NuxtLink to="/login" class="btn-primary">Log In to Continue</NuxtLink>
          </div>
        </div>

        <!-- Welcome State (No Messages) -->
        <div v-else-if="messages.length === 0" class="flex justify-center items-center h-full">
          <div class="text-center max-w-2xl px-4">
            <h3 class="text-2xl font-semibold text-gray-800 mb-4">Welcome, {{ authStore.user?.name?.split(' ')[0] || 'User' }}</h3>
            
            <!-- Input Area -->
            <div class="mb-8">
              <div class="relative max-w-2xl mx-auto">
                <textarea
                  v-model="userInput"
                  @keydown.enter.exact.prevent="sendMessage"
                  @keydown.enter.shift.exact="addNewLine"
                  rows="1"
                  class="w-full border border-gray-300 rounded-2xl py-4 px-6 pr-16 focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent resize-none"
                  placeholder="What can I help you with today?"
                  :disabled="loading"
                  ref="messageInput"
                ></textarea>
                <button
                  type="submit"
                  @click="sendMessage"
                  :disabled="loading || !userInput.trim()"
                  :class="[
                    'absolute right-3 bottom-3 p-2 rounded-full transition-colors',
                    userInput.trim() && !loading 
                      ? 'bg-green-600 hover:bg-green-700 text-white' 
                      : 'bg-gray-200 text-gray-400 cursor-not-allowed'
                  ]"
                >
                  <svg v-if="loading" class="w-5 h-5 animate-spin" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                  </svg>
                  <svg v-else class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"></path>
                  </svg>
                </button>
              </div>
            </div>
            
            <!-- Suggestion Cards -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 max-w-4xl mx-auto">
              <button 
                @click="sendMessage('Plan an event for me')"
                class="bg-gray-50 border border-gray-200 rounded-lg p-4 hover:bg-gray-100 transition-colors text-left"
              >
                <h4 class="font-medium text-gray-900 mb-1">Plan an event for me</h4>
              </button>
              <button 
                @click="sendMessage('Help me with my sales breakdown')"
                class="bg-gray-50 border border-gray-200 rounded-lg p-4 hover:bg-gray-100 transition-colors text-left"
              >
                <h4 class="font-medium text-gray-900 mb-1">Help me with my sales breakdown</h4>
              </button>
              <button 
                @click="sendMessage('I need vendors around me')"
                class="bg-gray-50 border border-gray-200 rounded-lg p-4 hover:bg-gray-100 transition-colors text-left"
              >
                <h4 class="font-medium text-gray-900 mb-1">I need vendors around me</h4>
              </button>
            </div>
          </div>
        </div>

        <!-- Chat Messages -->
        <div v-else class="flex flex-col h-full">
          <!-- Conversation Header -->
          <div class="border-b border-gray-200 bg-white p-4">
            <div class="flex items-center justify-between">
              <div class="flex items-center space-x-4">
                <h1 class="text-xl font-semibold text-gray-900">
                  {{ getCurrentChatTitle() }}
                </h1>
              </div>
              <div class="flex items-center space-x-3">
                <!-- Share Button -->
                <button 
                  @click="showShareModal = true"
                  class="p-2 hover:bg-gray-100 rounded transition-colors"
                  title="Share conversation"
                >
                  <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8.684 13.342C8.886 12.938 9 12.482 9 12c0-.482-.114-.938-.316-1.342m0 2.684a3 3 0 110-2.684m0 2.684l6.632 3.316m-6.632-6l6.632-3.316m0 0a3 3 0 105.367-2.684 3 3 0 00-5.367 2.684zm0 9.316a3 3 0 105.367 2.684 3 3 0 00-5.367-2.684z"></path>
                  </svg>
                </button>
                <!-- More Options -->
                <button 
                  @click="showOptionsMenu = !showOptionsMenu"
                  class="p-2 hover:bg-gray-100 rounded transition-colors relative"
                >
                  <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 5v.01M12 12v.01M12 19v.01M12 6a1 1 0 110-2 1 1 0 010 2zm0 7a1 1 0 110-2 1 1 0 010 2zm0 7a1 1 0 110-2 1 1 0 010 2z"></path>
                  </svg>
                </button>
              </div>
            </div>
          </div>

          <!-- Messages Container -->
          <div class="flex-1 overflow-y-auto p-6">
            <div class="max-w-4xl mx-auto space-y-6">
              <div v-for="(message, index) in messages" :key="index">
                <!-- User message -->
                <div v-if="message.role === 'user'" class="flex justify-end">
                  <div class="bg-green-600 text-white rounded-2xl py-3 px-4 max-w-[70%] shadow-sm">
                    <p class="whitespace-pre-wrap">{{ message.content }}</p>
                  </div>
                </div>
                
                <!-- Assistant message -->
                <div v-else class="flex justify-start">
                  <div class="flex space-x-3 max-w-[80%]">
                    <div class="w-8 h-8 bg-green-600 rounded-full flex items-center justify-center flex-shrink-0 mt-1">
                      <span class="text-white text-xs font-medium">M</span>
                    </div>
                    <div class="bg-gray-50 rounded-2xl py-3 px-4 shadow-sm">
                      <p class="whitespace-pre-wrap text-gray-800">{{ message.content }}</p>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Loading indicator -->
              <div v-if="loading" class="flex justify-start">
                <div class="flex space-x-3 max-w-[80%]">
                  <div class="w-8 h-8 bg-green-600 rounded-full flex items-center justify-center flex-shrink-0 mt-1">
                    <span class="text-white text-xs font-medium">M</span>
                  </div>
                  <div class="bg-gray-50 rounded-2xl py-3 px-4 shadow-sm">
                    <div class="flex items-center space-x-2">
                      <div class="w-2 h-2 bg-gray-400 rounded-full animate-bounce"></div>
                      <div class="w-2 h-2 bg-gray-400 rounded-full animate-bounce" style="animation-delay: 0.2s"></div>
                      <div class="w-2 h-2 bg-gray-400 rounded-full animate-bounce" style="animation-delay: 0.4s"></div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- FAQ Content -->
      <div v-else-if="activeTab === 'faq'" class="flex-1 overflow-y-auto p-6">
        <div class="max-w-4xl mx-auto">
          <div class="text-center mb-8">
            <h2 class="text-3xl font-bold text-gray-900 mb-4">Frequently Asked Questions</h2>
            <p class="text-gray-600">Whether you're trying to plan your first event, or just understand how MaaMaa works, we're here for you. Check out common questions below, or send a message directly.</p>
          </div>

          <!-- Search FAQ -->
          <div class="mb-8">
            <div class="relative">
              <input
                type="text"
                placeholder="Search FAQs..."
                class="w-full border border-gray-300 rounded-lg py-3 px-4 pl-10 focus:outline-none focus:ring-2 focus:ring-primary-green focus:border-transparent"
              />
              <svg class="absolute left-3 top-3.5 h-5 w-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
              </svg>
            </div>
          </div>

          <!-- FAQ Items -->
          <div class="space-y-4">
            <div v-for="(faq, index) in faqItems" :key="index" class="border border-gray-200 rounded-lg">
              <button 
                @click="faq.open = !faq.open"
                class="w-full px-6 py-4 text-left flex justify-between items-center hover:bg-gray-50 focus:outline-none"
              >
                <h3 class="font-medium text-gray-900">{{ faq.question }}</h3>
                <svg 
                  :class="['w-5 h-5 text-gray-500 transition-transform', faq.open ? 'rotate-180' : '']"
                  fill="none" stroke="currentColor" viewBox="0 0 24 24"
                >
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path>
                </svg>
              </button>
              <div v-if="faq.open" class="px-6 pb-4">
                <p class="text-gray-600">{{ faq.answer }}</p>
              </div>
            </div>
          </div>

          <!-- Contact Section -->
          <div class="mt-12 text-center bg-pale-green rounded-lg p-8">
            <h3 class="text-xl font-semibold text-gray-900 mb-4">Still can't find what you're looking for?</h3>
            <p class="text-gray-600 mb-6">Send us a message directly and we'll help you out.</p>
            <button 
              @click="activeTab = 'chat'"
              class="btn-primary"
            >
              Start Chat
            </button>
          </div>
        </div>
      </div>

      <!-- Profile Content -->
      <div v-else-if="activeTab === 'profile'" class="flex-1 overflow-y-auto p-6">
        <div class="max-w-2xl mx-auto">
          <h2 class="text-3xl font-bold text-gray-900 mb-8">My Profile</h2>
          
          <div class="bg-white rounded-lg border border-gray-200 p-6">
            <!-- Profile Picture -->
            <div class="flex items-center space-x-6 mb-8">
              <div class="w-20 h-20 bg-primary-green rounded-full flex items-center justify-center">
                <span class="text-white text-2xl font-bold">
                  {{ authStore.user?.name?.charAt(0).toUpperCase() || 'U' }}
                </span>
              </div>
              <div>
                <h3 class="text-xl font-semibold text-gray-900">{{ authStore.user?.name || 'User' }}</h3>
                <p class="text-gray-600">{{ authStore.user?.email || 'user@example.com' }}</p>
                <p class="text-sm text-gray-500">{{ authStore.user?.plan || 'Free plan' }}</p>
              </div>
            </div>

            <!-- Profile Form -->
            <form class="space-y-6">
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Full Name</label>
                <input
                  type="text"
                  :value="authStore.user?.name || ''"
                  class="w-full border border-gray-300 rounded-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-primary-green focus:border-transparent"
                />
              </div>
              
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Email</label>
                <input
                  type="email"
                  :value="authStore.user?.email || ''"
                  class="w-full border border-gray-300 rounded-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-primary-green focus:border-transparent"
                />
              </div>
              
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Phone</label>
                <input
                  type="tel"
                  placeholder="+234 xxx xxx xxxx"
                  class="w-full border border-gray-300 rounded-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-primary-green focus:border-transparent"
                />
              </div>
              
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Location</label>
                <input
                  type="text"
                  placeholder="Lagos, Nigeria"
                  class="w-full border border-gray-300 rounded-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-primary-green focus:border-transparent"
                />
              </div>

              <div class="flex space-x-4">
                <button type="submit" class="btn-primary">
                  Save Changes
                </button>
                <button type="button" class="px-6 py-3 border border-gray-300 rounded-lg text-gray-700 hover:bg-gray-50 transition-colors">
                  Cancel
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>

      <!-- Settings Content -->
      <div v-else-if="activeTab === 'settings'" class="flex-1 overflow-y-auto p-6">
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
                  <button class="relative inline-flex h-6 w-11 items-center rounded-full bg-primary-green transition-colors focus:outline-none focus:ring-2 focus:ring-primary-green focus:ring-offset-2">
                    <span class="translate-x-6 inline-block h-4 w-4 transform rounded-full bg-white transition-transform"></span>
                  </button>
                </div>
                
                <div class="flex items-center justify-between">
                  <div>
                    <p class="font-medium text-gray-900">Marketing Emails</p>
                    <p class="text-sm text-gray-600">Receive tips and updates about African catering</p>
                  </div>
                  <button class="relative inline-flex h-6 w-11 items-center rounded-full bg-gray-200 transition-colors focus:outline-none focus:ring-2 focus:ring-primary-green focus:ring-offset-2">
                    <span class="translate-x-1 inline-block h-4 w-4 transform rounded-full bg-white transition-transform"></span>
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
                  <select class="w-full border border-gray-300 rounded-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-primary-green focus:border-transparent">
                    <option>English</option>
                    <option>Yoruba</option>
                    <option>Igbo</option>
                    <option>Hausa</option>
                    <option>Pidgin English</option>
                  </select>
                </div>
                
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Response Style</label>
                  <select class="w-full border border-gray-300 rounded-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-primary-green focus:border-transparent">
                    <option>Professional</option>
                    <option>Friendly</option>
                    <option>Detailed</option>
                    <option>Concise</option>
                  </select>
                </div>
              </div>
            </div>

            <!-- Data & Privacy -->
            <div class="bg-white rounded-lg border border-gray-200 p-6">
              <h3 class="text-lg font-semibold text-gray-900 mb-4">Data & Privacy</h3>
              <div class="space-y-4">
                <button class="w-full text-left px-4 py-3 border border-gray-200 rounded-lg hover:bg-gray-50 transition-colors">
                  <p class="font-medium text-gray-900">Export Chat History</p>
                  <p class="text-sm text-gray-600">Download all your conversations</p>
                </button>
                
                <button class="w-full text-left px-4 py-3 border border-gray-200 rounded-lg hover:bg-gray-50 transition-colors">
                  <p class="font-medium text-gray-900">Clear All Chats</p>
                  <p class="text-sm text-gray-600">Permanently delete all conversation history</p>
                </button>
                
                <button class="w-full text-left px-4 py-3 border border-red-200 rounded-lg hover:bg-red-50 transition-colors text-red-600">
                  <p class="font-medium">Delete Account</p>
                  <p class="text-sm">Permanently delete your account and all data</p>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- Chat Input (only show when there are messages) -->
      <div v-if="messages.length > 0" class="border-t border-gray-200 bg-white p-4">
        <div class="max-w-4xl mx-auto">
          <form @submit.prevent="sendMessage" v-if="authStore.user">
            <div class="flex items-end space-x-3">
              <div class="flex-1 relative">
                <textarea
                  v-model="userInput"
                  @keydown.enter.exact.prevent="sendMessage"
                  @keydown.enter.shift.exact="addNewLine"
                  rows="1"
                  class="w-full border border-gray-300 rounded-2xl py-3 px-4 pr-12 focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent resize-none"
                  placeholder="Message MaaMaa..."
                  :disabled="loading"
                  ref="messageInput"
                ></textarea>
                <button
                  type="submit"
                  :disabled="loading || !userInput.trim()"
                  :class="[
                    'absolute right-2 bottom-2 p-2 rounded-full transition-colors',
                    userInput.trim() && !loading 
                      ? 'bg-green-600 hover:bg-green-700 text-white' 
                      : 'bg-gray-200 text-gray-400 cursor-not-allowed'
                  ]"
                >
                  <svg v-if="loading" class="w-5 h-5 animate-spin" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                  </svg>
                  <svg v-else class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"></path>
                  </svg>
                </button>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>

    <!-- Modals and Overlays -->
    <!-- Share Modal -->
    <div v-if="showShareModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50" @click="showShareModal = false">
      <div @click.stop class="bg-white rounded-lg p-6 w-full max-w-md mx-4">
        <div class="flex justify-between items-center mb-6">
          <h3 class="text-xl font-semibold">Jollof Event For 150 Guests</h3>
          <button @click="showShareModal = false" class="text-gray-400 hover:text-gray-600">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
            </svg>
          </button>
        </div>
        
        <!-- Chat Summary -->
        <div class="bg-gray-50 rounded-lg p-4 mb-6">
          <p class="text-sm text-gray-600 mb-2">I want to make jollof rice for 150 guests</p>
          <div class="text-xs text-gray-500">
            <p><strong>Cooking steps scaled for 150 people:</strong></p>
            <p>1. Parboil 15 kg of rice for 10 min, rinse & set aside.</p>
            <p>2. Blend tomatoes, peppers & onions into smooth paste.</p>
            <p>3. Heat 3L oil, fry blend for 20 min with spices.</p>
            <p>4. Add 5L stock, season well.</p>
            <p>5. Stir in rice, cover & steam till done (30-40 min).</p>
            <p>6. Keep fluffing so it stays vibrant & not soggy.</p>
          </div>
        </div>

        <!-- Share Options -->
        <div class="flex justify-center space-x-4 mb-4">
          <button class="p-3 border border-gray-200 rounded-lg hover:bg-gray-50 transition-colors">
            <svg class="w-6 h-6 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"></path>
            </svg>
            <span class="sr-only">Copy link</span>
          </button>
          <button class="p-3 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors">
            <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
              <path d="M24 4.557c-.883.392-1.832.656-2.828.775 1.017-.609 1.798-1.574 2.165-2.724-.951.564-2.005.974-3.127 1.195-.897-.957-2.178-1.555-3.594-1.555-3.179 0-5.515 2.966-4.797 6.045-4.091-.205-7.719-2.165-10.148-5.144-1.29 2.213-.669 5.108 1.523 6.574-.806-.026-1.566-.247-2.229-.616-.054 2.281 1.581 4.415 3.949 4.89-.693.188-1.452.232-2.224.084.626 1.956 2.444 3.379 4.6 3.419-2.07 1.623-4.678 2.348-7.29 2.04 2.179 1.397 4.768 2.212 7.548 2.212 9.142 0 14.307-7.721 13.995-14.646.962-.695 1.797-1.562 2.457-2.549z"/>
            </svg>
            <span class="sr-only">Share to X</span>
          </button>
          <button class="p-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors">
            <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
              <path d="M24 12.073c0-6.627-5.373-12-12-12s-12 5.373-12 12c0 5.99 4.388 10.954 10.125 11.854v-8.385H7.078v-3.47h3.047V9.43c0-3.007 1.792-4.669 4.533-4.669 1.312 0 2.686.235 2.686.235v2.953H15.83c-1.491 0-1.956.925-1.956 1.874v2.25h3.328l-.532 3.47h-2.796v8.385C19.612 23.027 24 18.062 24 12.073z"/>
            </svg>
            <span class="sr-only">Share to LinkedIn</span>
          </button>
        </div>

        <div class="text-center">
          <button class="text-blue-600 hover:text-blue-700 text-sm font-medium">Share to LinkedIn</button>
        </div>
      </div>
    </div>

    <!-- User Menu Dropdown -->
    <div v-if="showUserMenu" class="fixed inset-0 z-40" @click="showUserMenu = false">
      <div 
        class="absolute bottom-24 left-4 bg-white rounded-lg shadow-lg border border-gray-200 py-2 w-64"
        @click.stop
      >
        <div class="px-4 py-3 border-b border-gray-200">
          <p class="text-sm font-medium text-gray-900">{{ authStore.user?.name || 'User' }}</p>
          <p class="text-xs text-gray-500">{{ authStore.user?.plan || 'Free plan' }}</p>
        </div>
        <button 
          @click="activeTab = 'settings'; showUserMenu = false"
          class="w-full px-4 py-2 text-left hover:bg-gray-50 flex items-center space-x-3"
        >
          <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"></path>
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
          </svg>
          <span>Settings</span>
        </button>
        <button class="w-full px-4 py-2 text-left hover:bg-gray-50 flex items-center space-x-3">
          <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8.228 9c.549-1.165 2.03-2 3.772-2 2.21 0 4 1.343 4 3 0 1.4-1.278 2.575-3.006 2.907-.542.104-.994.54-.994 1.093m0 3h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
          </svg>
          <span>Get help</span>
        </button>
        <button class="w-full px-4 py-2 text-left hover:bg-gray-50 flex items-center space-x-3">
          <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M9 19l3 3m0 0l3-3m-3 3V10"></path>
          </svg>
          <span>Upgrade plan</span>
        </button>
        <div class="border-t border-gray-200 my-2"></div>
        <button 
          @click="logout"
          class="w-full px-4 py-2 text-left hover:bg-gray-50 flex items-center space-x-3 text-red-600"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"></path>
          </svg>
          <span>Log out</span>
        </button>
      </div>
    </div>

    <!-- Options Menu -->
    <div v-if="showOptionsMenu" class="fixed inset-0 z-40" @click="showOptionsMenu = false">
      <div 
        class="absolute top-16 right-4 bg-white rounded-lg shadow-lg border border-gray-200 py-2 w-48"
        @click.stop
      >
        <button 
          @click="clearCurrentChat; showOptionsMenu = false"
          class="w-full px-4 py-2 text-left hover:bg-gray-50 flex items-center space-x-3"
        >
          <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path>
          </svg>
          <span>Clear Chat</span>
        </button>
        <button 
          @click="exportChat; showOptionsMenu = false"
          class="w-full px-4 py-2 text-left hover:bg-gray-50 flex items-center space-x-3"
        >
          <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path>
          </svg>
          <span>Export Chat</span>
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
const userInput = ref('');
const messages = ref([]);
const loading = ref(false);
const chatContainer = ref(null);
const messageInput = ref(null);

// UI State
const sidebarCollapsed = ref(false);
const showShareModal = ref(false);
const showUserMenu = ref(false);
const showOptionsMenu = ref(false);
const activeTab = ref('chat');

// Chat Management
const currentChatId = ref(null);
const chatHistory = ref([
  {
    id: '1',
    title: 'Jollof Event for 150 Guests',
    updatedAt: new Date('2024-12-15'),
    messages: []
  },
  {
    id: '2', 
    title: 'Coconut Rice High Class Event',
    updatedAt: new Date('2024-12-14'),
    messages: []
  }
]);

// Navigation tabs
const navigationTabs = [
  {
    key: 'chat',
    label: 'Chat',
    icon: ChatIcon
  },
  {
    key: 'faq',
    label: 'Get Help',
    icon: QuestionMarkCircleIcon
  },
  {
    key: 'profile',
    label: 'My Profile',
    icon: UserIcon
  },
  {
    key: 'settings',
    label: 'Settings',
    icon: CogIcon
  }
];

// Share functionality
const shareLink = ref('https://maamaa.com/chat/shared/abc123');

// FAQ data
const faqItems = ref([
  {
    question: "How do I plan a new event on MaaMaa?",
    answer: "MaaMaa makes it easy. Just enter 'Plan Event' on your chatbox, enter your guest count and dishes, and we'll handle the rest — from portions to vendor suggestions.",
    open: false
  },
  {
    question: "Can MaaMaa help me find vendors?",
    answer: "Yes! MaaMaa can help connect you with verified vendors in your area. We can pull live quotes from 5 suppliers and help you compare prices if you have a preferred market.",
    open: false
  },
  {
    question: "Does MaaMaa understand local measurements?",
    answer: "Absolutely! MaaMaa works with local African measurements including kilograms, liters, congo (paint buckets), and derica measures. We understand the context of African cooking and markets.",
    open: false
  },
  {
    question: "Does MaaMaa have a mobile app?",
    answer: "Currently, MaaMaa is available as a web application that works great on mobile browsers. We're working on dedicated mobile apps that will be available soon.",
    open: false
  },
  {
    question: "Do I have to pay for everything?",
    answer: "MaaMaa offers both free and premium plans. The free plan includes basic event planning and recipe suggestions. Premium plans offer vendor connections, detailed shopping lists, and priority support.",
    open: false
  },
  {
    question: "What types of African cuisine does MaaMaa support?",
    answer: "MaaMaa supports cuisine from across Africa including West African (Nigerian, Ghanaian, Senegalese), East African (Ethiopian, Kenyan), and Southern African dishes. We're constantly expanding our recipe database.",
    open: false
  },
  {
    question: "Can I save my event plans?",
    answer: "Yes! All your event plans and conversations are automatically saved to your account. You can access them anytime from your chat history.",
    open: false
  },
  {
    question: "How accurate are the portion calculations?",
    answer: "Our portion calculations are based on extensive research of African eating habits and event patterns. We account for cultural factors like generous portions and multiple dishes, ensuring you have enough food for your guests.",
    open: false
  }
]);

// Suggested questions for users
const suggestions = [
  "How much food should I prepare for 50 guests?",
  "What are some popular Nigerian dishes for events?", 
  "Can you suggest a menu for a Ghanaian wedding?",
  "How do I calculate drinks for a party?"
];

// Dummy responses for the AI assistant
const dummyResponses = {
  "How much food should I prepare for 50 guests?": 
    "For 50 guests, I recommend planning for:\n\n- Main dishes: 25-30 pounds (or approximately 12-15 kg)\n- Side dishes: 12-15 pounds (or about 6-7.5 kg)\n- Rice or other starches: 10-12 pounds (or about 5-6 kg)\n\nIn Nigerian catering specifically, a good rule of thumb is to plan for about 0.5-0.75 pound of main dish protein per person, slightly more if you're serving bone-in meat. Remember that it's always better to have a bit extra than to run out!",
  
  "What are some popular Nigerian dishes for events?": 
    "Popular Nigerian dishes for events include:\n\n1. Jollof Rice - Essential for any Nigerian celebration\n2. Pounded Yam with Egusi Soup - A traditional favorite\n3. Moin Moin - Steamed bean pudding\n4. Suya - Spiced skewered meat\n5. Asaro (Yam Porridge) - Hearty and filling\n6. Chin Chin - Sweet fried pastry snacks\n7. Small Chops - Assortment of appetizers like puff puff, samosas, etc.\n8. Pepper Soup - Spicy meat or fish soup\n9. Plantain (fried or grilled) - Popular side dish\n\nThe most successful events typically feature Jollof Rice, a protein option (often chicken, beef, or fish), and at least 2-3 sides.",
  
  "Can you suggest a menu for a Ghanaian wedding?": 
    "For a Ghanaian wedding, here's a traditional menu suggestion:\n\n**Main Dishes:**\n- Waakye (rice and beans)\n- Jollof Rice\n- Banku with Okra Stew\n- Fufu with Light Soup or Groundnut Soup\n\n**Proteins:**\n- Grilled Tilapia\n- Chicken Stew\n- Kelewele (spiced fried plantains)\n- Beef Kebabs\n\n**Sides:**\n- Gari Foto\n- Kontomire Stew (cocoyam leaves)\n- Shito (hot pepper sauce)\n- Plantains (various preparations)\n\n**Desserts:**\n- Cake (often multi-tiered)\n- Traditional sweet treats like Bofrot (puff puff)\n\nThis menu offers a good balance of traditional Ghanaian dishes that work well for large celebrations.",
  
  "How do I calculate drinks for a party?": 
    "For calculating drinks at an African event:\n\n**General Guidelines:**\n- Plan for 1-2 drinks per person per hour\n- For a 4-hour event with 50 guests: 200-400 total drinks\n\n**Popular Drink Distribution:**\n- Water: 30% (always have plenty)\n- Soft drinks (Coca-Cola, Fanta, etc.): 25%\n- Beer: 20%\n- Wine: 15%\n- Spirits: 10%\n\n**Don't forget traditional drinks like:**\n- Zobo (Hibiscus drink)\n- Chapman\n- Palm Wine (for traditional events)\n- Sobolo\n\nConsider having more non-alcoholic options if your event includes many children or non-drinkers.",
};

// Generic responses for other queries
const genericResponses = [
  "That's a great question about African catering! Based on my knowledge, I'd suggest looking into traditional recipes that incorporate local spices and ingredients. Would you like me to suggest some specific dishes?",
  
  "When planning African cuisine for events, it's important to consider regional variations. West African food differs from East African cuisine in terms of spices and preparation methods. What specific region are you interested in?",
  
  "For your event, I recommend incorporating a mix of traditional dishes and fusion options to cater to different tastes. This approach has been very successful for many events I've helped plan.",
  
  "Portion sizes for African events tend to be generous. I typically recommend planning for about 1.5 times the usual portion size per person, especially for staple foods like rice and fufu.",
  
  "Based on my experience with similar events, I would suggest focusing on a few signature dishes done extremely well rather than many mediocre options. Quality over quantity always makes for a more memorable event.",
];

// Function to send message
const sendMessage = async (text = null) => {
  // Don't send empty messages
  const messageContent = text || userInput.value.trim();
  if (!messageContent || loading.value) return;
  
  // Add user message
  messages.value.push({
    role: 'user',
    content: messageContent,
  });
  
  // Clear input if it's from the text input (not a suggestion)
  if (!text) userInput.value = '';
  
  // Set loading state
  loading.value = true;
  
  // Simulate AI response delay
  await new Promise(resolve => setTimeout(resolve, 1500));
  
  // Generate AI response
  let aiResponse;
  
  // Check for predefined responses first
  if (dummyResponses[messageContent]) {
    aiResponse = dummyResponses[messageContent];
  } else {
    // Use a random generic response
    const randomIndex = Math.floor(Math.random() * genericResponses.length);
    aiResponse = genericResponses[randomIndex];
  }
  
  // Add AI response
  messages.value.push({
    role: 'assistant',
    content: aiResponse,
  });
  
  // Clear loading state
  loading.value = false;
  
  // Update current chat if it exists
  if (currentChatId.value) {
    updateCurrentChatHistory();
  } else {
    // Create new chat
    createNewChatFromMessages();
  }
  
  // Scroll to bottom after rendering
  nextTick(() => {
    scrollToBottom();
  });
};

// Chat management functions
const startNewChat = () => {
  messages.value = [];
  currentChatId.value = null;
  activeTab.value = 'chat';
};

const loadChat = (chat) => {
  currentChatId.value = chat.id;
  messages.value = [...chat.messages];
  nextTick(() => {
    scrollToBottom();
  });
};

const deleteChat = (chatId) => {
  chatHistory.value = chatHistory.value.filter(chat => chat.id !== chatId);
  if (currentChatId.value === chatId) {
    startNewChat();
  }
};

const clearCurrentChat = () => {
  messages.value = [];
  if (currentChatId.value) {
    const chatIndex = chatHistory.value.findIndex(chat => chat.id === currentChatId.value);
    if (chatIndex !== -1) {
      chatHistory.value[chatIndex].messages = [];
    }
  }
};

const createNewChatFromMessages = () => {
  if (messages.value.length > 0) {
    const newChat = {
      id: Date.now().toString(),
      title: generateChatTitle(messages.value[0].content),
      updatedAt: new Date(),
      messages: [...messages.value]
    };
    chatHistory.value.unshift(newChat);
    currentChatId.value = newChat.id;
  }
};

const updateCurrentChatHistory = () => {
  if (currentChatId.value) {
    const chatIndex = chatHistory.value.findIndex(chat => chat.id === currentChatId.value);
    if (chatIndex !== -1) {
      chatHistory.value[chatIndex].messages = [...messages.value];
      chatHistory.value[chatIndex].updatedAt = new Date();
    }
  }
};

const generateChatTitle = (firstMessage) => {
  // Generate a title from the first message
  if (firstMessage.length > 50) {
    return firstMessage.substring(0, 47) + '...';
  }
  return firstMessage;
};

const getCurrentChatTitle = () => {
  if (currentChatId.value) {
    const chat = chatHistory.value.find(chat => chat.id === currentChatId.value);
    return chat ? chat.title : 'Chat with MaaMaa';
  }
  return messages.value.length > 0 ? 'Chat with MaaMaa' : 'New Chat';
};

// Utility functions
const formatDate = (date) => {
  const now = new Date();
  const chatDate = new Date(date);
  const diffTime = Math.abs(now - chatDate);
  const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
  
  if (diffDays === 1) return 'Today';
  if (diffDays === 2) return 'Yesterday';
  if (diffDays <= 7) return `${diffDays} days ago`;
  
  return chatDate.toLocaleDateString();
};

const copyShareLink = async () => {
  try {
    await navigator.clipboard.writeText(shareLink.value);
    // You could add a toast notification here
  } catch (err) {
    console.error('Failed to copy: ', err);
  }
};

const exportChat = () => {
  const chatContent = messages.value.map(msg => 
    `${msg.role === 'user' ? 'You' : 'MaaMaa'}: ${msg.content}`
  ).join('\n\n');
  
  const blob = new Blob([chatContent], { type: 'text/plain' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `maamaa-chat-${Date.now()}.txt`;
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
  URL.revokeObjectURL(url);
};

const logout = () => {
  authStore.logout();
  showUserMenu.value = false;
  navigateTo('/');
};

const addNewLine = () => {
  userInput.value += '\n';
};

// Scroll chat to bottom
const scrollToBottom = () => {
  if (chatContainer.value) {
    chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
  }
};

// Auto-resize textarea
const autoResizeTextarea = () => {
  if (messageInput.value) {
    messageInput.value.style.height = 'auto';
    messageInput.value.style.height = messageInput.value.scrollHeight + 'px';
  }
};

// Watch for input changes to auto-resize
watch(userInput, () => {
  nextTick(() => {
    autoResizeTextarea();
  });
});

// Scroll to bottom when messages change
watch(messages, () => {
  nextTick(() => {
    scrollToBottom();
  });
}, { deep: true });

// Handle keyboard shortcuts
onMounted(() => {
  const handleKeydown = (e) => {
    // Ctrl/Cmd + K for new chat
    if ((e.ctrlKey || e.metaKey) && e.key === 'k') {
      e.preventDefault();
      startNewChat();
    }
    // Ctrl/Cmd + / for toggle sidebar
    if ((e.ctrlKey || e.metaKey) && e.key === '/') {
      e.preventDefault();
      sidebarCollapsed.value = !sidebarCollapsed.value;
    }
  };
  
  document.addEventListener('keydown', handleKeydown);
  
  // Cleanup
  onUnmounted(() => {
    document.removeEventListener('keydown', handleKeydown);
  });
});
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

/* Custom scrollbar for chat container */
.overflow-y-auto::-webkit-scrollbar {
  width: 6px;
}

.overflow-y-auto::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 3px;
}

.overflow-y-auto::-webkit-scrollbar-thumb {
  background: #c1c1c1;
  border-radius: 3px;
}

.overflow-y-auto::-webkit-scrollbar-thumb:hover {
  background: #a1a1a1;
}

/* Animation for sidebar */
.transition-all {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

/* Custom textarea resize */
textarea {
  resize: none;
  min-height: 44px;
  max-height: 120px;
}

/* Group hover effect for chat items */
.group:hover .group-hover\:opacity-100 {
  opacity: 1;
}

/* Smooth height transitions */
.h-screen {
  height: 100vh;
  height: 100dvh; /* For newer browsers that support dynamic viewport */
}

/* Modal backdrop */
.fixed.inset-0 {
  backdrop-filter: blur(4px);
}

/* Loading dots animation */
@keyframes bounce {
  0%, 80%, 100% { 
    transform: scale(0);
  } 40% { 
    transform: scale(1);
  }
}

.animate-bounce {
  animation: bounce 1.4s infinite ease-in-out both;
}

/* Custom focus styles */
.focus\:ring-2:focus {
  box-shadow: 0 0 0 2px rgba(0, 124, 46, 0.2);
}

/* Hover effects */
.hover\:bg-gray-800:hover {
  background-color: rgba(31, 41, 55, 1);
}

.hover\:bg-gray-100:hover {
  background-color: rgba(243, 244, 246, 1);
}

/* Message bubble styles */
.rounded-2xl {
  border-radius: 1rem;
}

/* Sidebar transition improvements */
@media (max-width: 768px) {
  .w-80 {
    width: 100%;
    position: absolute;
    z-index: 50;
    height: 100%;
  }
  
  .w-16 {
    width: 0;
    overflow: hidden;
  }
}

/* Better message spacing */
.space-y-6 > * + * {
  margin-top: 1.5rem;
}

/* Custom button active states */
.bg-gray-700 {
  background-color: rgba(55, 65, 81, 1);
}

/* Improved shadow for modals */
.shadow-lg {
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
}

/* Chat input improvements */
.rounded-2xl {
  border-radius: 1rem;
}

/* Better focus indicators */
.focus\:outline-none:focus {
  outline: 2px solid transparent;
  outline-offset: 2px;
}

/* Improved transitions */
.transition-colors {
  transition-property: color, background-color, border-color, text-decoration-color, fill, stroke;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
  transition-duration: 150ms;
}

/* Loading spinner styles */
.animate-spin {
  animation: spin 1s linear infinite;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

/* Backdrop blur for better modal experience */
.backdrop-blur {
  backdrop-filter: blur(8px);
}

/* Better text truncation */
.truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

/* Improved hover states for interactive elements */
.cursor-pointer:hover {
  transform: translateY(-1px);
  transition: transform 0.2s ease;
}

/* Custom scrollbar for sidebar */
.overflow-y-auto.sidebar-scroll::-webkit-scrollbar {
  width: 4px;
}

.overflow-y-auto.sidebar-scroll::-webkit-scrollbar-track {
  background: rgba(55, 65, 81, 0.1);
}

.overflow-y-auto.sidebar-scroll::-webkit-scrollbar-thumb {
  background: rgba(156, 163, 175, 0.5);
  border-radius: 2px;
}

/* Better mobile responsiveness */
@media (max-width: 640px) {
  .max-w-\[70\%\] {
    max-width: 85%;
  }
  
  .max-w-\[80\%\] {
    max-width: 90%;
  }
}

/* Additional utility classes for chat bubbles */
.max-w-\[70\%\] {
  max-width: 70%;
}

.max-w-\[80\%\] {
  max-width: 80%;
}
</style>
