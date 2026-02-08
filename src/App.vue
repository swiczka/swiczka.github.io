<script setup>
import FloatingFrame from './components/FloatingFrame.vue';
import HeartExplosion from './components/HeartExplosion.vue';
import * as THREE from 'three';
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
import { degToRad } from 'three/src/math/MathUtils';
import { radToDeg } from 'three/src/math/MathUtils';
import {ref, onMounted, onUnmounted} from 'vue';
const noClicked = ref(false);
const showNoButton = ref(true);
const isHappy = ref(false);
const heartEffect = ref(null);
const scenaContainer = ref(null);

const handleYesClick = () => {
  isHappy.value = true;
  heartEffect.value?.explode();
};

let scene, camera, renderer, cube;
let animationId;

onMounted(() => {

  const container = scenaContainer.value; 
  
  scene = new THREE.Scene();
  renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
  const width = container.clientWidth;
  const height = container.clientHeight;
  renderer.setSize(width, height);
  renderer.setClearColor( 0x000000, 0 );
  scene.background = null;


  camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000);
  camera.position.z = 6;
  camera.position.y = 4;
  camera.rotation.x = degToRad(-10);


  container.appendChild(renderer.domElement);

  const ambient = new THREE.AmbientLight(0xFFFFFF, 2);
  scene.add(ambient);

  const dirLight = new THREE.PointLight(0xFFFFFF, 4);
  dirLight.position.x = 2;
  dirLight.position.y = 2;
  dirLight.position.z = 2;
  scene.add(dirLight);

  var clock, mixer;
  clock = new THREE.Clock();
  const gltfLoader = new GLTFLoader();
  loadMan();

  function loadMan() {
    gltfLoader.load('/ja2.glb',
        function (gltf)  {
            const man = gltf.scene;

            man.traverse((child) => {
                if (child.isMesh) {
                  if(!child.material){
                      child.material = new THREE.MeshPhongMaterial({color: 0xFFFFFF, metalness: 0.0});
                    }
                    child.castShadow = true;
                    child.receiveShadow = true;
                    child.material.roughness = 0.8;
                    child.material.reflectivity = 0.15; 
                }
            });

            scene.add(man);

            if (gltf.animations && gltf.animations.length > 0) {
                mixer = new THREE.AnimationMixer(man);
                mixer.timeScale = 1;
                gltf.animations.forEach((clip) => {
                    const action = mixer.clipAction(clip);
                    action.loop = THREE.LoopRepeat;
                    action.play();
                });
            }
        }
    );
  }

  function animate() {

    renderer.render(scene, camera);
    if (mixer) mixer.update(clock.getDelta());
  }

  renderer.setAnimationLoop(animate);
});

// Funkcja do zmiany rozmiaru
const handleResize = () => {
  if (!scenaContainer.value) return;
  const width = scenaContainer.value.clientWidth;
  const height = scenaContainer.value.clientHeight;
  
  renderer.setSize(width, height);
  camera.aspect = width / height;
  camera.updateProjectionMatrix();
};

// Sprzątanie po sobie (Ważne w SPA!)
onUnmounted(() => {
  cancelAnimationFrame(animationId);
  window.removeEventListener('resize', handleResize);
  if (renderer) renderer.dispose();
});

</script>

<template>
  <body class="bg-[radial-gradient(145.05%_100%_at_50%_0%,#d28dfc_0%,#fc8dc5_57.38%,#fc8db1_88.16%)] text-white">
    <div class="min-h-screen flex flex-col font-sans text-gray-800">

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

        <div ref="scenaContainer" class="w-full" style="height: 600px;">

        </div>

        <div>
          <FloatingFrame image-url="https://ireland.apollo.olxcdn.com/v1/files/5ol3nsfpmeqs1-PL/image;s=1000x750" popup-emoji="👊" popup-text="ŻÓŁTE AUTO" x="-57" y="72"/>
          <FloatingFrame image-url="my.jpg" popup-emoji="🥰" popup-text="spk jesteś" x="60" y="72"/>
          <FloatingFrame popup-emoji="😈" popup-text="good girl" x="10" y="53"/>
        </div>
      </main>

      <footer class="bg-pink-900 text-pink-100 p-4 text-center text-sm">
        Copyright 2026 Grodzisko Dolne Industries
      </footer>

    </div>
  </body>
</template>

<style scoped></style>
