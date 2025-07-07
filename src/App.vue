<script setup lang="ts">

import { ref } from 'vue'
import { model } from './firebase.ts'

//userQuestion stores what the user types in the box
const userQuestion = ref('')

//responseText stores what Gemini sends back
const responseText = ref('')

const loading = ref(false)


// Wrap in an async function so you can use await
async function submitQuestion() {
  if (!userQuestion.value.trim()) return

  loading.value = true
  responseText.value = ''

  try {
    const result = await model.generateContent(userQuestion.value)
    const text = result.response.text()
    responseText.value = text
  } catch (error) {
    responseText.value = 'Something went wrong. Check the console.'
    console.error('Gemini error:', error)
  } finally {
    loading.value = false
  }
}

//run()
</script>

<template>
  <div class="godot-icon">
    <img src="@/assets/godoticon.svg" alt="Godot Icon" />
  </div>
  <div class="app-container">
    <h1>Godot 4 Game Dev Assistant</h1>

    <div class="question-box">
      <textarea
        v-model="userQuestion"
        placeholder="Ask a question about Godot 4 game development..."
        rows="4"
      ></textarea>

      <button @click="submitQuestion" :disabled="loading">Ask Gemini {{ loading ? 'Thinking...' : ''}}</button>
    </div>

    <div v-if="responseText" class="response-box">
      <h2>Response:</h2>
      <p>{{ responseText }}</p>
    </div>
  </div>
</template>

<style scoped>

.godot-icon {
  justify-self: center;
  scale: 1.5;
}

.app-container {
  max-width: 700px;
  margin: 3rem auto;
  padding: 2rem;
  border-radius: 12px;
  background: #1e1e2f;
  color: white;
  font-family: sans-serif;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.2);
}

h1 {
  text-align: center;
  margin-bottom: 2rem;
}

.question-box {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

textarea {
  width: 100%;
  padding:1rem;
  font-size: 1rem;
  border-radius: 8px;
  border: none;
  resize: vertical;
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
  padding: 1rem;
  border-radius: 8px;
}
</style>
