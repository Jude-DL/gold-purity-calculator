<template>
  <div v-if="isVisible" class="modal-overlay" @click="closeModal">
    <div class="modal-content" @click.stop>
      <h2>Login</h2>
      <form @submit.prevent="handleLogin">
        <div class="form-group">
          <label for="username">Username:</label>
          <input id="username" type="text" v-model="username" required>
        </div>
        <div class="form-group">
          <label for="password">Password:</label>
          <input id="password" type="password" v-model="password" required>
        </div>
        <button type="submit" class="login-btn">Login</button>
        <button type="button" @click="closeModal" class="cancel-btn">Cancel</button>
      </form>
      <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const isVisible = ref(false)
const username = ref('')
const password = ref('')
const errorMessage = ref('')

const openModal = () => {
  isVisible.value = true
  errorMessage.value = ''
}

const closeModal = () => {
  isVisible.value = false
  username.value = ''
  password.value = ''
  errorMessage.value = ''
}

const handleLogin = () => {
  // Simple mock login - in a real app, this would call an API
  if (username.value === 'admin' && password.value === 'password') {
    // Emit login success event with username
    emit('login-success', username.value)
    closeModal()
  } else {
    errorMessage.value = 'Invalid username or password'
  }
}

// Define emits
const emit = defineEmits(['login-success'])

// Expose functions to parent
defineExpose({
  openModal,
  closeModal
})
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  width: 300px;
  max-width: 90%;
  z-index: 1001;
  pointer-events: auto;
}

h2 {
  margin-top: 0;
  text-align: center;
}

.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: 500;
}

input {
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

.login-btn, .cancel-btn {
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-right: 10px;
}

.login-btn {
  background-color: #007bff;
  color: white;
}

.login-btn:hover {
  background-color: #0056b3;
}

.cancel-btn {
  background-color: #6c757d;
  color: white;
}

.cancel-btn:hover {
  background-color: #545b62;
}

.error {
  color: red;
  margin-top: 10px;
  text-align: center;
}
</style>
