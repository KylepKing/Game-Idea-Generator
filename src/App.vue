<script setup lang="ts">
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'

// Import the functions you need from the SDKs you need
import { initializeApp } from 'firebase/app'
//import { getAnalytics } from 'firebase/analytics'
import { getAI, getGenerativeModel, GoogleAIBackend } from 'firebase/ai'
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: 'AIzaSyADj2B2tgbg8jJjyPcpCSdLgxLEyExIC_0',
  authDomain: 'godot4-gamemaker-assistant.firebaseapp.com',
  projectId: 'godot4-gamemaker-assistant',
  storageBucket: 'godot4-gamemaker-assistant.firebasestorage.app',
  messagingSenderId: '705783289749',
  appId: '1:705783289749:web:36e59d03d1e9c54eccc89c',
  measurementId: 'G-P5Z0WYFZWQ',
}
// test

// Initialize Firebase
const app = initializeApp(firebaseConfig)
//const analytics = getAnalytics(app)

// Initialize the Gemini Developer API backend service
const ai = getAI(app, { backend: new GoogleAIBackend() })

// Create a `GenerativeModel` instance with a model that supports your use case
const model = getGenerativeModel(ai, { model: 'gemini-2.5-flash' })

// Wrap in an async function so you can use await
async function run() {
  // Provide a prompt that contains text
  const prompt = 'Write a story about a magic backpack.'

  // To generate text output, call generateContent with the text input
  const result = await model.generateContent(prompt)

  const response = result.response
  const text = response.text()
  console.log(text)
}

run()
</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />
    </div>
  </header>

  <main>
    <TheWelcome />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
