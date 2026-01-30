<script setup>
import { ref } from 'vue'
import GoldPurityCalculator from './components/GoldPurityCalculator.vue'
import LoginModal from './components/LoginModal.vue'
import RegisterModal from './components/RegisterModal.vue'

const loginModal = ref(null)
const registerModal = ref(null)
const isLoggedIn = ref(false)
const currentUser = ref('')

const openLoginModal = () => {
  loginModal.value.openModal()
}

const openRegisterModal = () => {
  registerModal.value.openModal()
}

const handleLogin = (user) => {
  isLoggedIn.value = true
  currentUser.value = user
}

const handleLogout = () => {
  isLoggedIn.value = false
  currentUser.value = ''
}

// Expose functions to modals
defineExpose({
  handleLogin
})
</script>

<template>
  <div id="app">
    <header>
      <nav class="navbar">
        <div class="navbar-brand">
          <h1>Gold Purity Calculator</h1>
        </div>
        <div class="navbar-links">
          <span v-if="isLoggedIn" class="welcome-user">Welcome, {{ currentUser }}!</span>
          <button v-if="!isLoggedIn" class="nav-button" @click="openRegisterModal">Register</button>
          <button v-if="!isLoggedIn" class="nav-button" @click="openLoginModal">Login</button>
          <button v-if="isLoggedIn" class="nav-button" @click="handleLogout">Logout</button>
        </div>
      </nav>
    </header>
    <main>
      <GoldPurityCalculator :isLoggedIn="isLoggedIn" @open-register="openRegisterModal" @open-login="openLoginModal" />
    </main>
    <LoginModal ref="loginModal" @login-success="handleLogin" />
    <RegisterModal ref="registerModal" @register-success="handleLogin" />
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

header {
  background-color: #007bff;
  color: white;
  padding: 0;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  max-width: 1200px;
  margin: 0 auto;
  padding: 1rem 2rem;
}

.navbar-brand h1 {
  margin: 0;
  font-size: 1.5rem;
}

.navbar-links {
  display: flex;
  gap: 1rem;
}

.nav-button {
  background-color: transparent;
  border: 1px solid white;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.nav-button:hover {
  background-color: rgba(255, 255, 255, 0.1);
}

main {
  padding: 20px;
}
</style>
