<template>
  <div class="min-h-screen bg-gradient-to-br from-primary-green to-dark-green flex items-center justify-center px-4">
    <div class="max-w-md w-full text-center text-white">
      <!-- Maintenance Illustration -->
      <div class="mb-8">
        <div class="mx-auto w-32 h-32 bg-white/20 backdrop-blur-sm rounded-full flex items-center justify-center mb-6">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 text-white animate-pulse" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
          </svg>
        </div>
        <div class="mb-6">
          <img src="/images/maamaa-logo.png" alt="MaaMaa Logo" class="h-12 mx-auto opacity-90" />
        </div>
      </div>

      <!-- Maintenance Content -->
      <div class="mb-8">
        <h1 class="text-3xl md:text-4xl font-bold mb-4">
          We're Under Maintenance
        </h1>
        <p class="text-white/90 mb-6 text-lg">
          We're currently performing scheduled maintenance to improve your experience. We'll be back shortly!
        </p>
        
        <!-- Estimated time -->
        <div class="bg-white/10 backdrop-blur-sm border border-white/20 rounded-lg p-4 mb-6">
          <div class="flex items-center justify-center mb-2">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            <span class="font-semibold">Estimated Completion</span>
          </div>
          <p class="text-xl font-bold">{{ estimatedCompletion }}</p>
        </div>
      </div>

      <!-- Progress indicator -->
      <div class="mb-8">
        <div class="flex items-center justify-between text-sm mb-2">
          <span>Maintenance Progress</span>
          <span>{{ progressPercentage }}%</span>
        </div>
        <div class="w-full bg-white/20 rounded-full h-2">
          <div 
            class="bg-white h-2 rounded-full transition-all duration-500 ease-out"
            :style="{ width: `${progressPercentage}%` }"
          ></div>
        </div>
      </div>

      <!-- What's being improved -->
      <div class="mb-8">
        <h3 class="text-lg font-semibold mb-4">What we're improving:</h3>
        <ul class="space-y-2 text-left text-white/90">
          <li class="flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-3 text-light-green" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            Enhanced AI catering recommendations
          </li>
          <li class="flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-3 text-light-green" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            Improved performance and speed
          </li>
          <li class="flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-3 text-light-green" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            New inventory management features
          </li>
          <li class="flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-3 text-light-green" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            Security and reliability updates
          </li>
        </ul>
      </div>

      <!-- Contact and updates -->
      <div class="space-y-4">
        <div class="bg-white/10 backdrop-blur-sm border border-white/20 rounded-lg p-4">
          <h4 class="font-semibold mb-2">Stay Updated</h4>
          <p class="text-sm text-white/90 mb-3">
            Get notified when we're back online
          </p>
          <div class="flex space-x-2">
            <input
              v-model="notificationEmail"
              type="email"
              placeholder="your@email.com"
              class="flex-1 px-3 py-2 bg-white/10 border border-white/30 rounded-lg text-white placeholder-white/60 focus:outline-none focus:ring-2 focus:ring-white/50"
            />
            <button
              @click="subscribeToUpdates"
              :disabled="isSubscribing"
              class="bg-white/20 hover:bg-white/30 disabled:bg-white/10 px-4 py-2 rounded-lg font-medium transition duration-200"
            >
              {{ isSubscribing ? 'Sending...' : 'Notify Me' }}
            </button>
          </div>
        </div>

        <!-- Social links -->
        <div class="flex justify-center space-x-6">
          <a href="#" class="text-white/70 hover:text-white transition duration-200" aria-label="Facebook">
            <svg class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24">
              <path fill-rule="evenodd" d="M22 12c0-5.523-4.477-10-10-10S2 6.477 2 12c0 4.991 3.657 9.128 8.438 9.878v-6.987h-2.54V12h2.54V9.797c0-2.506 1.492-3.89 3.777-3.89 1.094 0 2.238.195 2.238.195v2.46h-1.26c-1.243 0-1.63.771-1.63 1.562V12h2.773l-.443 2.89h-2.33v6.988C18.343 21.128 22 16.991 22 12z" clip-rule="evenodd" />
            </svg>
          </a>
          <a href="#" class="text-white/70 hover:text-white transition duration-200" aria-label="Twitter">
            <svg class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24">
              <path d="M8.29 20.251c7.547 0 11.675-6.253 11.675-11.675 0-.178 0-.355-.012-.53A8.348 8.348 0 0022 5.92a8.19 8.19 0 01-2.357.646 4.118 4.118 0 001.804-2.27 8.224 8.224 0 01-2.605.996 4.107 4.107 0 00-6.993 3.743 11.65 11.65 0 01-8.457-4.287 4.106 4.106 0 001.27 5.477A4.072 4.072 0 012.8 9.713v.052a4.105 4.105 0 003.292 4.022 4.095 4.095 0 01-1.853.07 4.108 4.108 0 003.834 2.85A8.233 8.233 0 012 18.407a11.616 11.616 0 006.29 1.84" />
            </svg>
          </a>
          <a href="#" class="text-white/70 hover:text-white transition duration-200" aria-label="Instagram">
            <svg class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24">
              <path fill-rule="evenodd" d="M12.315 2c2.43 0 2.784.013 3.808.06 1.064.049 1.791.218 2.427.465a4.902 4.902 0 011.772 1.153 4.902 4.902 0 011.153 1.772c.247.636.416 1.363.465 2.427.048 1.067.06 1.407.06 4.123v.08c0 2.643-.012 2.987-.06 4.043-.049 1.064-.218 1.791-.465 2.427a4.902 4.902 0 01-1.153 1.772 4.902 4.902 0 01-1.772 1.153c-.636.247-1.363.416-2.427.465-1.067.048-1.407.06-4.123.06h-.08c-2.643 0-2.987-.012-4.043-.06-1.064-.049-1.791-.218-2.427-.465a4.902 4.902 0 01-1.772-1.153 4.902 4.902 0 01-1.153-1.772c-.247-.636-.416-1.363-.465-2.427-.047-1.024-.06-1.379-.06-3.808v-.63c0-2.43.013-2.784.06-3.808.049-1.064.218-1.791.465-2.427a4.902 4.902 0 011.153-1.772A4.902 4.902 0 015.45 2.525c.636-.247 1.363-.416 2.427-.465C8.901 2.013 9.256 2 11.685 2h.63zm-.081 1.802h-.468c-2.456 0-2.784.011-3.807.058-.975.045-1.504.207-1.857.344-.467.182-.8.398-1.15.748-.35.35-.566.683-.748 1.15-.137.353-.3.882-.344 1.857-.047 1.023-.058 1.351-.058 3.807v.468c0 2.456.011 2.784.058 3.807.045.975.207 1.504.344 1.857.182.466.399.8.748 1.15.35.35.683.566 1.15.748.353.137.882.3 1.857.344 1.054.048 1.37.058 4.041.058h.08c2.597 0 2.917-.01 3.96-.058.976-.045 1.505-.207 1.858-.344.466-.182.8-.398 1.15-.748.35-.35.566-.683.748-1.15.137-.353.3-.882.344-1.857.048-1.055.058-1.37.058-4.041v-.08c0-2.597-.01-2.917-.058-3.96-.045-.976-.207-1.505-.344-1.858a3.097 3.097 0 00-.748-1.15 3.098 3.098 0 00-1.15-.748c-.353-.137-.882-.3-1.857-.344-1.023-.047-1.351-.058-3.807-.058zM12 6.865a5.135 5.135 0 110 10.27 5.135 5.135 0 010-10.27zm0 1.802a3.333 3.333 0 100 6.666 3.333 3.333 0 000-6.666zm5.338-3.205a1.2 1.2 0 110 2.4 1.2 1.2 0 010-2.4z" clip-rule="evenodd" />
            </svg>
          </a>
        </div>

        <!-- Emergency contact -->
        <p class="text-sm text-white/70">
          Urgent issues? Email us at 
          <a href="mailto:support@maamaa.com" class="text-light-green hover:text-white">support@maamaa.com</a>
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
// Set page meta
definePageMeta({
  layout: false // Use no layout for maintenance page
});

// Set page title and description
useHead({
  title: 'Under Maintenance | MaaMaa',
  meta: [
    {
      name: 'description',
      content: 'MaaMaa is currently under maintenance. We are working to improve your experience and will be back shortly.'
    }
  ]
});

// Reactive data
const notificationEmail = ref('');
const isSubscribing = ref(false);
const progressPercentage = ref(0);

// Estimated completion time (you can make this dynamic)
const estimatedCompletion = ref('2 hours');

// Simulate maintenance progress
onMounted(() => {
  // Start with some initial progress
  progressPercentage.value = 25;
  
  // Simulate progress updates
  const progressInterval = setInterval(() => {
    if (progressPercentage.value < 85) {
      progressPercentage.value += Math.random() * 5;
    }
  }, 10000); // Update every 10 seconds
  
  // Clean up interval after 5 minutes
  setTimeout(() => {
    clearInterval(progressInterval);
  }, 300000);
});

// Subscribe to maintenance updates
const subscribeToUpdates = async () => {
  if (!notificationEmail.value) {
    alert('Please enter your email address');
    return;
  }
  
  if (!isValidEmail(notificationEmail.value)) {
    alert('Please enter a valid email address');
    return;
  }
  
  isSubscribing.value = true;
  
  try {
    // Simulate API call to subscribe user
    await new Promise(resolve => setTimeout(resolve, 1000));
    
    // Here you would typically call your backend API
    console.log('Subscribing email for maintenance updates:', notificationEmail.value);
    
    alert('Thank you! We\'ll notify you when we\'re back online.');
    notificationEmail.value = '';
    
  } catch (error) {
    console.error('Failed to subscribe:', error);
    alert('Failed to subscribe. Please try again later.');
  } finally {
    isSubscribing.value = false;
  }
};

// Email validation helper
const isValidEmail = (email) => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
};

// Auto-refresh page every 5 minutes to check if maintenance is complete
onMounted(() => {
  const refreshInterval = setInterval(() => {
    // In a real scenario, you might check a status endpoint
    // For now, we'll just refresh the page
    window.location.reload();
  }, 300000); // 5 minutes
  
  // Clean up on unmount
  onUnmounted(() => {
    clearInterval(refreshInterval);
  });
});
</script>
