<script setup>
import { ref, computed, onMounted } from 'vue';

// --- KONFIGURACJA ---
// Możesz podmienić URL na swój własny obrazek w assets
// 1. Zamiast 'const' z wpisanymi na sztywno danymi, używamy defineProps
const props = defineProps({
  // Nazwa argumentu: Typ danych
  imageUrl: {
    type: String,
    default: 'https://i.pinimg.com/736x/a3/a4/4f/a3a44f18ff478fa8abb15c93a979a811.jpg' // Wartość domyślna (jakbyś zapomniał podać)
  },
  popupEmoji: {
    type: String,
    default: '🥀'
  },
  popupText: {
    type: String,
    default: 'hej'
  },
  x: {
    type: Number,
    default: 30
  },
  y: {
    type: Number,
    default: 50
  }
});
const displayTimeMs = 2500; // Jak długo tekst jest widoczny (2.5 sekundy)

// --- STAN (ZMIENNE REAKTYWNE) ---
const showMessage = ref(false);
const topPos = ref(0); // Pozycja wertykalna (vh)
const leftPos = ref(0); // Pozycja horyzontalna (vw)
let timerId = null; // Identyfikator timera, żeby móc go anulować

// --- FUNKCJE ---

// Funkcja obsługująca kliknięcie
const handleClick = () => {
  // Jeśli klikniemy szybko drugi raz, resetujemy poprzedni timer
  if (timerId) clearTimeout(timerId);

  showMessage.value = true;

  // Ustawiamy timer, który ukryje wiadomość po określonym czasie
  timerId = setTimeout(() => {
    showMessage.value = false;
    timerId = null;
  }, displayTimeMs);
};

// Funkcja obliczająca losową pozycję "po bokach"
const calculateRandomPosition = () => {
  leftPos.value = props.x;
  topPos.value = props.y;
};

// Computed property łączące zmienne w styl CSS
const positionStyle = computed(() => ({
  top: `${topPos.value}vh`,
  left: `${leftPos.value}vw`,
}));

// Gdy komponent się ładuje, oblicz pozycję
onMounted(() => {
  calculateRandomPosition();
});
</script>

<template>
  <div
    class="fixed z-50 cursor-pointer hover:scale-110 transition-transform duration-300 animate-float select-none"
    :style="positionStyle"
    @click="handleClick"
  >
    <div class="relative overflow-hidden bg-white p-2 rounded-xl rotate-3 shadow-[0_10px_20px_rgba(244,63,94,0.4)] border-[5px] border-rose-300">

      <img
        :src="props.imageUrl"
        alt="Walentynka"
        class="w-28 h-28 md:w-36 md:h-36 object-cover rounded-lg pointer-events-none"
      />

      <p class="mx-auto text-center">Kliknij tu</p>

      <Transition
        enter-active-class="transition ease-out duration-300"
        enter-from-class="opacity-0 scale-90"
        enter-to-class="opacity-100 scale-100"
        leave-active-class="transition ease-in duration-200"
        leave-from-class="opacity-100 scale-100"
        leave-to-class="opacity-0 scale-90"
      >
        <div
            v-if="showMessage"
            class="absolute inset-0 flex flex-col items-center justify-center p-2 rounded-lg text-white text-center font-bold shadow-inner"
            >
            <p class="text-xl text-red-700 uppercase tracking-widest mb-2">
            {{ popupText }}
            </p>

            <p class="text-6xl">
            {{ props.popupEmoji }}
            </p>

        </div>
      </Transition>

    </div>
  </div>
</template>

<style scoped>
/* Własna animacja pływania (delikatniejsza niż animate-bounce z Tailwinda) */
@keyframes float {
  0%, 100% {
    transform: translateY(0px) rotate(3deg);
  }
  50% {
    /* Przesunięcie w górę o 15px i lekka zmiana rotacji */
    transform: translateY(-15px) rotate(0deg);
  }
}

/* Klasa używająca animacji */
.animate-float {
  /* 6s trwania, nieskończona pętla, płynne przejście */
  animation: float 6s ease-in-out infinite;
  /* To sprawia, że animacja jest płynniejsza na GPU */
  will-change: transform;
}
</style>