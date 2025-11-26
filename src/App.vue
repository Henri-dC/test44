<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import axios from 'axios';

const weirdMessage = ref("Connecting to the other side...");
const floatingText = ref({ x: 50, y: 50 });
let intervalId = null;

const fetchWeirdness = async () => {
  try {
    const response = await axios.get('http://127.0.0.1:3000/api/weirdness');
    weirdMessage.value = response.data.message;
  } catch (error) {
    console.error("The void is silent:", error);
    weirdMessage.value = "///STATIC///";
  }
};

const updateFloatingTextPosition = () => {
  floatingText.value.x = Math.random() * 80 + 10;
  floatingText.value.y = Math.random() * 80 + 10;
};

onMounted(() => {
  fetchWeirdness();
  intervalId = setInterval(updateFloatingTextPosition, 2000);
});

onUnmounted(() => {
  if (intervalId) {
    clearInterval(intervalId);
  }
});
</script>

<template>
  <main class="bg-black text-lime-400 min-h-screen w-screen flex flex-col items-center justify-center p-4 font-mono overflow-hidden relative">
    
    <div 
      class="absolute text-red-500 text-9xl opacity-10"
      :style="{ top: `${floatingText.y}%`, left: `${floatingText.x}%`, transform: 'translate(-50%, -50%)' }"
    >
      ?
    </div>
    
    <div class="z-10 text-center">
      <h1 class="text-4xl md:text-6xl glitch mb-8">SOMETHING IS WRONG</h1>
      
      <p class="text-xl md:text-2xl mb-12 p-4 border border-lime-400 max-w-lg mx-auto floating">
        {{ weirdMessage }}
      </p>

      <button 
        @click="fetchWeirdness" 
        class="px-8 py-4 bg-lime-400 text-black border-2 border-lime-400 hover:bg-black hover:text-lime-400 transition-colors duration-300 focus:outline-none focus:ring-2 focus:ring-lime-600 active:translate-y-1"
      >
        PONDER THE ORB
      </button>
    </div>

    <div class="absolute bottom-4 right-4 text-sm text-gray-700">
      You can't escape.
    </div>
    <div class="absolute top-4 left-4 text-2xl text-purple-500 animate-pulse">
      [SYSTEM ERROR]
    </div>

  </main>
</template>

<style scoped>
/* Scoped styles can go here if needed, but main.css has the keyframes */
h1.glitch {
  animation: glitch 1s infinite alternate-reverse;
}
</style>
