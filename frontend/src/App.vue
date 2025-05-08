<template>
  <main class="min-h-screen bg-zinc-900 text-white font-mono p-4">
    <header class="text-center my-8">
      <h1 class="text-5xl font-bold graffiti-text drop-shadow-md">🎤 Keep It Real Simulator 🎤</h1>
      <p class="text-xl mt-4 max-w-2xl mx-auto italic">
        Are you "real" or just playing a part? Step into the cipher and find out.
      </p>
    </header>

    <!-- Persona Builder -->
    <section class="bg-zinc-800 rounded-2xl p-6 my-8 shadow-lg">
      <h2 class="text-3xl font-bold mb-4">🧠 Build Your Persona</h2>
      <div class="grid md:grid-cols-3 gap-4">
        <div>
          <label class="block text-sm">Choose Your Origin Story</label>
          <select v-model="origin" class="w-full p-2 bg-zinc-700 rounded">
            <option>Street Hustler</option>
            <option>Ivy League Poet</option>
            <option>Bedroom Beatmaker</option>
            <option>Industry Plant</option>
          </select>
        </div>
        <div>
          <label class="block text-sm">Pick Your Ethos</label>
          <select v-model="ethos" class="w-full p-2 bg-zinc-700 rounded">
            <option>Keep It Real</option>
            <option>Flex and Finesse</option>
            <option>Rebel Against the Machine</option>
            <option>Peace, Love, Unity</option>
          </select>
        </div>
        <div>
          <label class="block text-sm">Stage Name</label>
          <input v-model="stageName" class="w-full p-2 bg-zinc-700 rounded" placeholder="e.g., Lil Paradox" />
        </div>
      </div>
      <button @click="generatePersona" class="mt-4 bg-yellow-400 text-black px-4 py-2 rounded hover:bg-yellow-300 transition">
        Generate Persona Bio
      </button>
      <div v-if="personaBio" class="mt-4 text-zinc-300 border-t border-zinc-600 pt-4">
        <p><strong>{{ stageName }}</strong>: {{ personaBio }}</p>
      </div>
    </section>

    <!-- MCing Simulator -->
    <section class="bg-zinc-800 rounded-2xl p-6 my-8 shadow-lg">
      <h2 class="text-3xl font-bold mb-4">🎙️ Freestyle Challenge</h2>
      <p class="mb-2">Drop a bar based on this prompt:</p>
      <div class="italic text-yellow-300 mb-4">
        "{{ prompt }}"
      </div>
      <textarea
        v-model="freestyle"
        rows="3"
        class="w-full p-2 bg-zinc-700 rounded text-sm"
        placeholder="Spit something real... or fake it."
      ></textarea>
      <button @click="rateFreestyle" class="mt-3 bg-pink-500 px-4 py-2 rounded hover:bg-pink-400 transition">
        Rate My Bar
      </button>
      <div v-if="feedback" class="mt-4 text-lime-300 italic">{{ feedback }}</div>
    </section>

    <!-- Footer graffiti quote -->
    <footer class="text-center text-zinc-400 text-sm mt-12">
      <p class="graffiti-text text-lg mt-4 text-zinc-500">“Sometimes the fakest bar hits the hardest.”</p>
      <p>© 2025 The Realness Project</p>
    </footer>
  </main>
</template>

<script setup>
import { ref } from 'vue'

const origin = ref('Street Hustler')
const ethos = ref('Keep It Real')
const stageName = ref('')
const personaBio = ref('')

function generatePersona() {
  const combo = `${origin.value} with a mission to ${ethos.value.toLowerCase()}`
  personaBio.value = `A ${combo}, always walking the line between performance and truth.`
}

const freestyle = ref('')
const prompt = "You never lived the life you rap about—how do you justify your bars?"

const feedback = ref('')
function rateFreestyle() {
  if (!freestyle.value) {
    feedback.value = "Silence? Even silence tells a story..."
  } else if (freestyle.value.toLowerCase().includes('truth') || freestyle.value.toLowerCase().includes('mask')) {
    feedback.value = "🔥 Bars! You’re walking the tightrope of realness like a pro."
  } else if (freestyle.value.length > 100) {
    feedback.value = "You overcompensating, fam? Keep it tight, keep it true."
  } else {
    feedback.value = "Hmm… not sure if it’s real or just well-rehearsed. And that’s the point."
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Silkscreen&family=Permanent+Marker&display=swap');

.graffiti-text {
  font-family: 'Permanent Marker', cursive;
}
</style>
