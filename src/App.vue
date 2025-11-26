<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const weirdText = ref("The void stares back.");
const weirdStyle = ref({ color: '#39FF14' });
const weirdClass = ref('float');
const voidPosition = ref({ top: '50%', left: '50%' });
let audioCtx = null;

const fetchWeirdness = async () => {
  try {
    const response = await axios.get('http://127.0.0.1:3000/api/weirdness');
    const { text, color, animationClass } = response.data;
    weirdText.value = text;
    weirdStyle.value = { color: color };
    weirdClass.value = animationClass;
  } catch (error) {
    console.error("The simulation is broken:", error);
    weirdText.value = "///STATIC///";
    weirdStyle.value = { color: '#FF0000' };
    weirdClass.value = 'shake';
  }
};

const handleMouseMove = (event) => {
  voidPosition.value = { 
    top: `${event.clientY}px`, 
    left: `${event.clientX}px` 
  };
};

const probeAether = () => {
  if (!audioCtx) {
    audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  }
  const oscillator = audioCtx.createOscillator();
  const gainNode = audioCtx.createGain();

  oscillator.connect(gainNode);
  gainNode.connect(audioCtx.destination);

  oscillator.type = 'sawtooth';
  oscillator.frequency.setValueAtTime(440, audioCtx.currentTime); // A4
  oscillator.frequency.linearRampToValueAtTime(120, audioCtx.currentTime + 1.5); // Ramp down to ~A#2

  gainNode.gain.setValueAtTime(0.3, audioCtx.currentTime);
  gainNode.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 1.5);

  oscillator.start();
  oscillator.stop(audioCtx.currentTime + 1.5);
};


onMounted(() => {
  window.addEventListener('mousemove', handleMouseMove);
});
</script>

<template>
  <div class="flex flex-col items-center justify-center min-h-screen w-full overflow-hidden relative">
    
    <div 
      class="absolute bg-black rounded-full w-32 h-32 pointer-events-none -translate-x-1/2 -translate-y-1/2 blur-2xl"
      :style="voidPosition"
    ></div>

    <div class="text-center p-8 z-10">
      <h1 
        :class="weirdClass" 
        :style="weirdStyle" 
        class="text-4xl md:text-6xl mb-8 transition-colors duration-500"
      >
        {{ weirdText }}
      </h1>
      <div class="space-x-4">
        <button 
          @click="fetchWeirdness" 
          class="px-6 py-3 border-2 border-green-400 text-green-400 hover:bg-green-400 hover:text-black transition-all duration-300 glitch"
        >
          Request Anomaly
        </button>
        <button 
          @click="probeAether" 
          class="px-6 py-3 border-2 border-purple-400 text-purple-400 hover:bg-purple-400 hover:text-black transition-all duration-300"
        >
          Probe Aether
        </button>
      </div>
    </div>

  </div>
</template>

<style scoped>
/* Scoped styles can go here if needed */
</style>
