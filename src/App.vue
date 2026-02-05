<script setup>
import FloatingFrame from './components/FloatingFrame.vue';
import HeartExplosion from './components/HeartExplosion.vue';
import {ref} from 'vue';
const noClicked = ref(false);
const showNoButton = ref(true);
const isHappy = ref(false);
const heartEffect = ref(null);

const handleYesClick = () => {
  isHappy.value = true;
  heartEffect.value?.explode();
};

</script>

<template>
  <body class="bg-[radial-gradient(145.05%_100%_at_50%_0%,#d28dfc_0%,#fc8dc5_57.38%,#fc8db1_88.16%)] text-white">
    <div class="min-h-screen flex flex-col font-sans text-gray-800">

      <FloatingFrame image-url="https://ireland.apollo.olxcdn.com/v1/files/5ol3nsfpmeqs1-PL/image;s=1000x750" popup-emoji="👊" popup-text="ŻÓŁTE AUTO" x="57" y="72"/>
      <FloatingFrame image-url="my.jpg" popup-emoji="🥰" popup-text="spk jesteś" x="10" y="72"/>
      <FloatingFrame popup-emoji="😈" popup-text="good girl" x="43" y="53"/>

      <HeartExplosion ref="heartEffect" />

      <header class="bg-pink-900 p-3 text-center shadow-md">
        <h1 class="text-2xl font-bold text-white uppercase tracking-widest">
          UWAGA! 
          <p class="text-pink-300 text-xl"> NIEZWYKLE WAŻNE PYTANIE!!!!!</p>
        </h1>
      </header>

      <main class="flex-1 flex flex-col items-center justify-center p-6 gap-6">
        
        <h2 class="text-3xl font-bold text-pink-600 text-center" :class="noClicked ? 'uppercase' : ''">
          {{ isHappy ? "sooo ez 🥰" : "zostaniesz moją walentynką???" }}
        </h2>

        <div class="flex flex-col gap-4">
          <div class="gap-4 justify-center mx-auto" v-if="!isHappy"> 
            <button 
              @click="handleYesClick"
              class="bg-rose-500 hover:bg-rose-600 text-white px-8 py-3 mx-5 rounded-full font-semibold transition transform hover:scale-110 shadow-lg"
              :class="noClicked ? 'text-6xl' : 'text-xl'"
            >
              TAK
            </button>

            <button 
              v-if="showNoButton"
              @click="noClicked = true, showNoButton = false"
              
              class="bg-gray-200 hover:bg-gray-300 text-gray-700 px-8 py-3 mx-5 rounded-full text-xl font-semibold transition"
            >
              NIE
            </button>
          </div>
          <div class="text-pink-600 text-xl" v-if="noClicked && !isHappy">
            <p>that's okey diva każdemu zdarza się pomylić 🥰🥰</p>
          </div>
        </div>

        <div class="h-80">

        </div>
      </main>

      <footer class="bg-pink-900 text-pink-100 p-4 text-center text-sm">
        Copyright 2026 Grodzisko Dolne Industries
      </footer>

    </div>
  </body>
</template>

<style scoped></style>
