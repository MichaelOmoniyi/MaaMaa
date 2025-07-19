<template>
  <div class="h-screen w-screen flex flex-col overflow-hidden bg-white">
    <!-- Top Header -->
    <div class="flex items-center justify-between px-6 py-4 bg-white border-b border-gray-200 z-10">
      <!-- Left: Logo -->
      <div class="flex items-center space-x-4">
        <img src="/images/maamaa-logo.png" alt="MaaMaa" class="h-8 w-auto" />
      </div>
      
      <!-- Right: Notifications and Profile -->
      <div class="flex items-center space-x-3">
        <!-- Notifications -->
        <button class="p-2 hover:bg-gray-100 rounded-full transition-colors relative">
          <svg class="w-6 h-6 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 17h5l-5-5V9a6 6 0 00-6-6v0a6 6 0 00-6 6v3l-5 5h5a6 6 0 006 6v0a6 6 0 006-6z"></path>
          </svg>
          <!-- Notification badge -->
          <span class="absolute -top-1 -right-1 bg-red-500 text-white text-xs rounded-full h-5 w-5 flex items-center justify-center">3</span>
        </button>
        
        <!-- Profile Picture -->
        <div class="w-8 h-8 bg-green-600 rounded-full flex items-center justify-center">
          <span class="text-white text-sm font-medium">FO</span>
        </div>
      </div>
    </div>

    <!-- Main Layout -->
    <div class="flex-1 flex overflow-hidden">
      <!-- Sidebar -->
      <div :class="[
        'flex flex-col transition-all duration-300 relative',
        sidebarCollapsed ? 'w-16' : 'w-72',
        'bg-[#E6F7ED] border-r border-[#D1E7DD]'
      ]">
        <!-- Slot for sidebar content (e.g., chat history, nav) -->
        <slot name="sidebar" :sidebarCollapsed="sidebarCollapsed" :toggleSidebar="toggleSidebar" />
      </div>
      <!-- Main Content -->
      <div class="flex-1 flex flex-col bg-white">
        <slot :sidebarCollapsed="sidebarCollapsed" />
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  collapsed: {
    type: Boolean,
    default: false
  }
});

const emit = defineEmits(['update:collapsed']);

const sidebarCollapsed = ref(props.collapsed);

const toggleSidebar = () => {
  sidebarCollapsed.value = !sidebarCollapsed.value;
  emit('update:collapsed', sidebarCollapsed.value);
};

watch(() => props.collapsed, (newValue) => {
  sidebarCollapsed.value = newValue;
});
</script>
