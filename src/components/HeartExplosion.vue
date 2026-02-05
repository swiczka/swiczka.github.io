<script setup>
import { ref } from 'vue';

// 1. Konfiguracja
const emojis = ['❤️', '💖', '🥰', '😍', '✨']; // Możesz tu dodać cokolwiek!
const particles = ref([]); // Tablica przechowująca aktywne serduszka

// 2. Funkcja tworząca jedno serce
const createParticle = () => {
  const id = Date.now() + Math.random();
  const emoji = emojis[Math.floor(Math.random() * emojis.length)];
  
  // Losowe parametry dla każdego serduszka
  const particle = {
    id,
    emoji,
    left: Math.random() * 100 + 'vw',      // Pozycja w poziomie (0-100%)
    duration: Math.random() * 2 + 3 + 's', // Czas lotu (3 do 5 sekund)
    size: Math.random() * 2.5 + 1 + 'rem', // Rozmiar (1rem do 2.5rem)
    swing: Math.random() * 100 - 50 + 'px' // Lekkie kołysanie na boki
  };

  particles.value.push(particle);

  // Usuń serduszko z pamięci po zakończeniu animacji (żeby nie zapchać przeglądarki)
  setTimeout(() => {
    particles.value = particles.value.filter(p => p.id !== id);
  }, 5000); // 5s to max czas trwania animacji
};

// 3. Główna funkcja wywoływana z zewnątrz
const explode = () => {
  // Przez 2 sekundy, co 50 milisekund twórz nowe serce
  const interval = setInterval(createParticle, 30);

  // Po 2 sekundach przestań tworzyć nowe
  setTimeout(() => {
    clearInterval(interval);
  }, 2000);
};

// 4. WAŻNE: Udostępniamy funkcję explode dla rodzica (App.vue)
defineExpose({ explode });
</script>

<template>
  <div class="fixed inset-0 pointer-events-none z-50 overflow-hidden">
    
    <div
      v-for="p in particles"
      :key="p.id"
      class="heart-particle absolute bottom-[-50px]"
      :style="{
        left: p.left,
        fontSize: p.size,
        animationDuration: p.duration,
        '--swing': p.swing
      }"
    >
      {{ p.emoji }}
    </div>

  </div>
</template>

<style scoped>
/* Animacja lotu do góry */
@keyframes floatUp {
  0% {
    transform: translateY(0) translateX(0) rotate(0deg);
  }
  100% {
    /* Lecimy do góry (poza ekran) i trochę na bok (--swing) */
    transform: translateY(-110vh) translateX(var(--swing)) rotate(360deg);
  }
}

.heart-particle {
  /* will-change pomaga przeglądarce płynnie animować */
  will-change: transform;
  animation-name: floatUp;
  animation-timing-function: linear; /* Stała prędkość */
  animation-fill-mode: forwards;
}
</style>