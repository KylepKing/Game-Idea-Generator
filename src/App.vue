<script setup lang="ts">

import { ref, computed } from 'vue'
import { model } from './firebase.ts'
import { marked } from 'marked'

// Dropdown options
const mediums = ['2D', '3D', 'Text-Based']
const genres = ['Platformer', 'RPG', 'Puzzle', 'Shooter', 'Card Game']
const themes = ['Fantasy', 'Cyberpunk', 'Post-Apocalyptic', 'Sci-Fi', 'Steampunk']

// User selections
const selectedMedium = ref('')
const selectedGenre = ref('')
const selectedTheme = ref('')

// AI response and loading
const responseText = ref('')
const loading = ref(false)
const expanded = ref(false)

// Randomizers
function randomizeMedium() {
  selectedMedium.value = mediums[Math.floor(Math.random() * mediums.length)]
}
function randomizeGenre() {
  selectedGenre.value = genres[Math.floor(Math.random() * genres.length)]
}
function randomizeTheme() {
  selectedTheme.value = themes[Math.floor(Math.random() * themes.length)]
}
function randomizeAll() {
  randomizeMedium()
  randomizeGenre()
  randomizeTheme()
}

// Wrap in an async function so you can use await
async function submitQuestion() {
  if (!selectedMedium.value || !selectedGenre.value || !selectedTheme.value) return

  loading.value = true
  responseText.value = ''

  const prompt = `
You are a creative game designer.

Generate a unique and fun video game idea using these inputs:
- Medium: ${selectedMedium.value}
- Genre: ${selectedGenre.value}
- Theme: ${selectedTheme.value}

Include:
- A core gameplay loop (including main mechanic and basics of how that system works)
- A short story/setting
- Art style
- Optional characters or power-ups
-At the end provice a short list of steps to take to start building the game. Do not include any code just an outline of steps.

Keep it concise but imaginative.
`.trim()


  try {
    const result = await model.generateContent(prompt)
    const text = result.response.text()
    responseText.value = text
  } catch (error) {
    responseText.value = 'Something went wrong. Check the console.'
    console.error('Gemini error:', error)
  } finally {
    loading.value = false
  }
}

function resetApp() {
  window.location.reload()
}

function toggleExpanded() {
  expanded.value = !expanded.value
}

// Computed preview text (e.g. first 300 characters)
const previewText = computed(() =>
  responseText.value.length > 300
    ? responseText.value.slice(0, 300) + '...'
    : responseText.value
)

</script>

<template>

<div class="header">
  <h1>
    Game Idea Generator
  </h1>
</div>
   <!-- NEW SELECT INTERFACE -->
<div class="select-group">
  <label>Medium</label>
  <select v-model="selectedMedium">
    <option disabled value="">-- Select Medium --</option>
    <option v-for="m in mediums" :key="m" :value="m">{{ m }}</option>
  </select>
  <button @click="randomizeMedium">🎲</button>
</div>

<div class="select-group">
  <label>Genre</label>
  <select v-model="selectedGenre">
    <option disabled value="">-- Select Genre --</option>
    <option v-for="g in genres" :key="g" :value="g">{{ g }}</option>
  </select>
  <button @click="randomizeGenre">🎲</button>
</div>

<div class="select-group">
  <label>Theme</label>
  <select v-model="selectedTheme">
    <option disabled value="">-- Select Theme --</option>
    <option v-for="t in themes" :key="t" :value="t">{{ t }}</option>
  </select>
  <button @click="randomizeTheme">🎲</button>
</div>

<!-- Randomize All + Submit -->
<div class="button-container">
  <div class="main-buttons">
    <button @click="randomizeAll">🎲 Randomize All</button>
    <button @click="submitQuestion" :disabled="loading">
      Ask Gemini {{ loading ? 'Thinking...' : '' }}
    </button>
  </div>
  <button @click="resetApp" class="reset-button">
    🔄 Reset
  </button>
</div>
<div v-if="responseText" class="response-box">
  <h2>Response:</h2>
  <div @click="toggleExpanded" class="response-toggle">
    <div class="response-preview" v-html="marked( expanded ? responseText : previewText )">
    </div>
    <span class="dropdown-arrow">{{ expanded ? '▲ Show Less' : '▼ Show More' }}</span>
  </div>
</div>

</template>

<style scoped>

.header {
  text-align: center;
  margin-bottom: 2rem;
  color : #51a2ff;
  font-style: oblique;
}

.button-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  margin-top: 2rem;
}

.main-buttons {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
  justify-content: center;
}

.reset-button {
  padding: 0.8rem 1.5rem;
  background-color: #9ca3af;
  color: #1e1e2f;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
  transition: background-color 0.2s;
}

.reset-button:hover {
  background-color: #6b7280;
}

button {
  padding: 0.8rem 1.5rem;
  background-color: #4f46e5;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
  align-self: flex-start;
  transition: background-color 0.2s;
}

button:hover {
  background-color: #3730a3;
}

.response-box {
  margin-top: 2rem;
  background: #2d2d44;
  padding: 1.5rem;
  border-radius: 8px;
  white-space: pre-wrap;
  line-height: 1.5;
}

.response-box h2 {
  margin-bottom: 1rem;
  font-size: 1.25rem;
  color: #51a2ff;
}

.response-box pre {
  white-space: pre-wrap;
  font-family: monospace;
  color: #e0e0e0;
}
</style>
