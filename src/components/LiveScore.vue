<template>
  <div class="card is-shadow is-rounded p-4">
    <p class="card-header-title is-size-6 has-text-weight-semibold has-text-dark">
      {{ scoreData?.title || 'Live Score' }}
    </p>

    <div class="card-content">
      <div v-if="loading" class="has-text-centered has-text-grey">
        <span class="is-size-6">Loading live data...</span>
      </div>

      <div v-else-if="error" class="notification is-danger is-light has-text-centered">
        {{ error }}
      </div>

      <div
        v-else-if="scoreData?.livescore === 'Data Not Found'"
        class="notification is-warning is-light has-text-centered is-size-5"
      >
        🚫 Currently no live Data
      </div>

      <div v-else-if="scoreData">
        <p class="is-size-5 has-text-weight-bold has-text-primary mb-2">
          🏏 {{ scoreData.livescore }}
        </p>
        <p class="is-size-6 has-text-grey-dark mb-3">➡ {{ scoreData.runrate }}</p>
        <p class="has-text-grey is-size-6 mt-5">🏏 {{ scoreData.batterone }} - {{ scoreData.batsmanonerun }} {{ scoreData.batsmanoneball }}</p>
        <p class="has-text-grey is-size-6">🥎 {{ scoreData.bowlerone }} - {{ scoreData.bowleroneover }} Overs, {{ scoreData.bowleronerun }} Runs {{ scoreData.bowleronewickers }} Wickets</p>
        <p class="has-text-grey is-size-6 mt-3 mb-3">➡ {{ scoreData.update }}</p>
        </div>
    </div>

    <footer class="card-footer" v-if="scoreData && scoreData.livescore !== 'Data Not Found'">
      <button class="card-footer-item button is-danger is-fullwidth" @click="fetchScore">
        🔄 Refresh Score
      </button>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const API_URL = import.meta.env.VITE_SCORE_API

const scoreData = ref(null)
const loading = ref(false)
const error = ref(null)

const fetchScore = async () => {
  loading.value = true
  error.value = null
  try {
    const res = await fetch(API_URL)

    if (!res.ok) throw new Error('Failed to fetch score data')

    const data = await res.json()

    if (!('title' in data) || !('livescore' in data)) {
      throw new Error('Invalid data format')
    }

    scoreData.value = data
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}

onMounted(fetchScore)
</script>

<style scoped>
.card {
  border-radius: 15px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
  background: #fff;
  font-family: "Rubik", sans-serif;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.3);
}
.button {
  font-family: "Rubik", sans-serif;
  border: none;
  border-radius: 10px;
  font-size: 1.2rem;
  font-weight: 700;
  color: white;
  transition: background-color 0.3s ease;
}
.button:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}
.title {
  font-family: "Rubik", sans-serif;
  font-weight: 500;
  font-size: 18px;
}
p {
  font-family: "Rubik", sans-serif;
  font-weight: 500;
}
.post-content {
  font-family: "Rubik", sans-serif;
  line-height: 1.6;
}
.notification {
  font-family: "Rubik", sans-serif;
  font-weight: 500;
  font-size: 14px;
}
</style>