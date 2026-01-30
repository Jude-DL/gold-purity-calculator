<template>
  <div class="container">
    <div class="header">
      <h1>Gold Purity Calculator</h1>
      <p class="subtitle">Calculate gold prices with VAT for different karat purities</p>
    </div>

    <!-- Calculator Grid -->
    <div class="calculators-grid">
      <div v-for="(price, karat) in goldPrices" :key="karat" class="calculator-card">
        <div class="card-header">
          <h3>{{ karat }}K Gold</h3>
          <span class="purity-badge">{{ ((karat / 24) * 100).toFixed(1) }}% Pure</span>
        </div>

        <div class="price-display">
          <span class="price-label">Price per Gram</span>
          <span class="price-value">₱{{ price.toLocaleString('en-PH', { minimumFractionDigits: 2, maximumFractionDigits: 2 }) }}</span>
        </div>

        <form @submit.prevent="calculate(karat)" class="calculator-form">
          <div class="form-group">
            <label :for="'grams-' + karat">Weight (grams)</label>
            <input
              :id="'grams-' + karat"
              type="number"
              v-model.number="inputs[karat].grams"
              step="0.01"
              min="0.01"
              placeholder="Enter weight"
              required
            >
          </div>

          <div class="form-group">
            <label :for="'makingCharge-' + karat">Making Charge (₱)</label>
            <input
              :id="'makingCharge-' + karat"
              type="number"
              v-model.number="inputs[karat].makingCharge"
              step="0.01"
              min="0"
              placeholder="Enter charge"
              required
            >
          </div>

          <button type="submit" class="calculate-btn">
            <span>Calculate</span>
          </button>
        </form>

        <transition name="fade">
          <div v-if="results[karat]" class="results">
            <div class="result-item">
              <span class="result-label">Subtotal</span>
              <span class="result-value">₱{{ results[karat].goldRateBeforeVat.toLocaleString('en-PH', { minimumFractionDigits: 2, maximumFractionDigits: 2 }) }}</span>
            </div>
            <div class="result-item">
              <span class="result-label">VAT (12%)</span>
              <span class="result-value">₱{{ results[karat].vat.toLocaleString('en-PH', { minimumFractionDigits: 2, maximumFractionDigits: 2 }) }}</span>
            </div>
            <div class="result-item total">
              <span class="result-label">Total</span>
              <span class="result-value">₱{{ results[karat].totalGoldRate.toLocaleString('en-PH', { minimumFractionDigits: 2, maximumFractionDigits: 2 }) }}</span>
            </div>
          </div>
        </transition>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

// Define props and emits
const props = defineProps({
  isLoggedIn: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['open-register', 'open-login'])

// Gold prices per gram in PHP (current prices)
const goldPrices = {
  24: 9877.90,
  23: 9466.32,
  22: 9054.74,
  21: 8643.16,
  20: 8231.58,
  18: 7408.42,
  14: 5762.11,
  10: 4115.79
}

const inputs = ref({})
const results = ref({})

// Initialize inputs for each karat
Object.keys(goldPrices).forEach(karat => {
  inputs.value[karat] = { grams: 1, makingCharge: 0 }
})

const calculate = (karat) => {
  if (!props.isLoggedIn) {
    emit('open-register')
    return
  }

  const currentPrice = goldPrices[karat]
  const grams = inputs.value[karat].grams
  const makingCharge = inputs.value[karat].makingCharge
  const goldRateBeforeVat = currentPrice * grams + makingCharge
  const vat = goldRateBeforeVat * 0.12
  const totalGoldRate = goldRateBeforeVat + vat

  results.value[karat] = {
    goldRateBeforeVat,
    vat,
    totalGoldRate
  }
}
</script>

<style scoped>
* {
  box-sizing: border-box;
}

.container {
  max-width: 1400px;
  margin: 0 auto;
  padding: 2rem;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: linear-gradient(135deg, #f5f7fa 0%, #e9ecef 100%);
  min-height: 100vh;
}

.header {
  text-align: center;
  margin-bottom: 3rem;
}

h1 {
  font-size: 2.5rem;
  font-weight: 700;
  color: #1a1a1a;
  margin: 0 0 0.5rem 0;
  background: linear-gradient(135deg, #d4af37 0%, #ffd700 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.subtitle {
  color: #6c757d;
  font-size: 1rem;
  margin: 0;
}

.calculators-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.5rem;
}

.calculator-card {
  background: white;
  border-radius: 16px;
  padding: 1.5rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.07), 0 1px 3px rgba(0, 0, 0, 0.06);
  transition: all 0.3s ease;
  border: 1px solid rgba(0, 0, 0, 0.05);
}

.calculator-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.12), 0 4px 8px rgba(0, 0, 0, 0.08);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 1rem;
  border-bottom: 2px solid #f0f0f0;
}

.card-header h3 {
  margin: 0;
  font-size: 1.5rem;
  font-weight: 700;
  color: #1a1a1a;
}

.purity-badge {
  background: linear-gradient(135deg, #d4af37 0%, #ffd700 100%);
  color: #1a1a1a;
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  font-size: 0.75rem;
  font-weight: 600;
}

.price-display {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 1rem;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  border-radius: 12px;
  margin-bottom: 1.5rem;
}

.price-label {
  font-size: 0.875rem;
  color: #6c757d;
  margin-bottom: 0.25rem;
}

.price-value {
  font-size: 1.5rem;
  font-weight: 700;
  color: #28a745;
}

.calculator-form {
  margin-bottom: 1.5rem;
}

.form-group {
  margin-bottom: 1rem;
}

label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 600;
  color: #495057;
  font-size: 0.875rem;
}

input {
  width: 100%;
  padding: 0.75rem;
  border: 2px solid #e9ecef;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.2s ease;
  background: white;
}

input:focus {
  outline: none;
  border-color: #d4af37;
  box-shadow: 0 0 0 3px rgba(212, 175, 55, 0.1);
}

input::placeholder {
  color: #adb5bd;
}

.calculate-btn {
  width: 100%;
  padding: 0.875rem;
  background: linear-gradient(135deg, #d4af37 0%, #ffd700 100%);
  color: #1a1a1a;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 600;
  transition: all 0.2s ease;
  box-shadow: 0 2px 4px rgba(212, 175, 55, 0.3);
}

.calculate-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(212, 175, 55, 0.4);
}

.calculate-btn:active {
  transform: translateY(0);
}

.results {
  background: linear-gradient(135deg, #f8f9fa 0%, #fff 100%);
  border-radius: 12px;
  padding: 1rem;
  border-left: 4px solid #d4af37;
}

.result-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem 0;
}

.result-item:not(:last-child) {
  border-bottom: 1px solid #e9ecef;
}

.result-label {
  font-size: 0.875rem;
  color: #6c757d;
  font-weight: 500;
}

.result-value {
  font-size: 1rem;
  font-weight: 600;
  color: #1a1a1a;
}

.result-item.total {
  margin-top: 0.5rem;
  padding-top: 1rem;
  border-top: 2px solid #d4af37;
  border-bottom: none;
}

.result-item.total .result-label {
  font-size: 1rem;
  color: #1a1a1a;
  font-weight: 700;
}

.result-item.total .result-value {
  font-size: 1.25rem;
  color: #28a745;
  font-weight: 700;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

.login-required {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 400px;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  border-radius: 16px;
  margin: 2rem 0;
}

.login-message {
  text-align: center;
  max-width: 400px;
  padding: 2rem;
  background: white;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.07);
}

.login-message h3 {
  margin: 0 0 1rem 0;
  color: #d4af37;
  font-size: 1.5rem;
}

.login-message p {
  margin: 0 0 1.5rem 0;
  color: #6c757d;
  font-size: 1rem;
}

.auth-buttons {
  display: flex;
  gap: 1rem;
  justify-content: center;
}

.auth-btn {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 600;
  transition: all 0.2s ease;
}

.register-btn {
  background: linear-gradient(135deg, #28a745 0%, #20c997 100%);
  color: white;
}

.register-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(40, 167, 69, 0.4);
}

.login-btn {
  background: linear-gradient(135deg, #007bff 0%, #6610f2 100%);
  color: white;
}

.login-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 123, 255, 0.4);
}

/* Responsive Design */
@media (max-width: 1200px) {
  .calculators-grid {
    grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  }
}

@media (max-width: 768px) {
  .container {
    padding: 1rem;
  }
  
  h1 {
    font-size: 2rem;
  }
  
  .calculators-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
  }
  
  .calculator-card {
    padding: 1.25rem;
  }
}

@media (max-width: 480px) {
  h1 {
    font-size: 1.75rem;
  }
  
  .subtitle {
    font-size: 0.875rem;
  }
}
</style>