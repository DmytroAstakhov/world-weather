<script setup>
import { computed, ref } from 'vue'

const location = ref('')
const temperature = ref(0)
const description = ref('')
const loading = ref(false)
const error = ref(false)
const searchQuery = ref('')

const weatherSearch = () => {
  loading.value = true
  error.value = false
  fetch(
    `http://api.weatherapi.com/v1/current.json?key=dae3e383984741ea9bf191808251607&q=${searchQuery.value}`,
  )
    .then((response) => response.json())
    .then((data) => {
      loading.value = false
      location.value = data.location.name
      temperature.value = data.current.temp_c
      description.value = data.current.condition.text
      ResetSearchQuery()
    })
    .catch((error) => {
      loading.value = false
      error = true
      console.log(error)
    })
}

const ResetSearchQuery = () => {
  searchQuery.value = ''
}

const weatherClass = computed(() => {
  const desc = description.value.toLowerCase().trim()

  if (desc === 'sunny') return 'sunny'
  if (desc === 'overcast') return 'overcast'
  if (desc === 'partly cloudy') return 'partly-cloudy'
  if (desc === 'сloudy') return 'сloudy'
  return ''
})
</script>

<template>
  <div class="weather" :class="weatherClass">
    <div class="container">
      <div class="card weather-form">
        <input
          type="text"
          class="weather-form__input"
          placeholder="Enter city"
          v-model="searchQuery"
          @keyup.enter="weatherSearch"
        />
        <button class="weather-form__button" @click="weatherSearch">Search</button>
      </div>

      <div class="card weather-load" v-if="loading">Loading...</div>

      <div class="weather-info" v-show="!error && location && temperature !== 0 && description">
        <div class="card" v-if="error">Error</div>

        <div class="weather-info__text">
          <p class="card">{{ location }}</p>
          <p class="card">{{ temperature }} ℃</p>
          <p class="card">{{ description }}</p>
        </div>
      </div>
    </div>
    <div class="weather-bg">
      <div>
        <img class="weather-bg__img bg" src="/bg.png" alt="App Background" />
        <img class="weather-bg__img sunny" src="/sunny.png" alt="Sunny" />
        <img class="weather-bg__img partly-cloudy" src="/partly-cloudy.png" alt="partly-cloudy" />
        <img class="weather-bg__img overcast" src="/overcast.png" alt="overcast" />
        <img class="weather-bg__img сloudy" src="/сloudy.png" alt="сloudy" />
      </div>
    </div>
  </div>
</template>

<style>
.weather {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
}

.container {
  width: 100%;
  max-width: 1000px;
  margin: 0 auto;
  padding-left: 15px;
  padding-right: 15px;
  display: grid;
  grid-template-columns: 1fr 100px 200px;
  gap: 20px;
  box-sizing: border-box;
}

.card {
  background: rgba(255, 255, 255, 0.85);
  backdrop-filter: blur(6px);
  border-radius: var(--border-radius);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.05);
  padding: 20px 30px;
  box-sizing: border-box;
  color: #333333;
}

.weather-form {
  display: flex;
  align-items: stretch;
  gap: 20px;
}

.weather-form__input {
  flex-grow: 1;
  font-size: 20px;
  border: var(--width-line) solid var(--accent-rgb);
  border-radius: var(--border-radius);
  padding: 10px 15px;
  box-sizing: border-box;
}

.weather-form__input:focus {
  //border-color: var(--accent);
  //outline: none;
  outline-color: var(--accent);
}

.weather-form__button {
  flex-basis: 180px;
  font-size: 20px;
  background-color: #a8ddf0;
  color: #1b3f5d;
  border: none;
  border-radius: var(--border-radius);
  padding: 10px 15px;
  box-sizing: border-box;
  cursor: pointer;
  transition: var(--transition);
}

.weather-form__button:hover {
  background-color: var(--accent);
}

.weather-bg {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  margin: 0 auto;
  width: 100%;
  height: 100%;
  z-index: -1;
}

.weather-bg > div {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
}

.weather-bg__img {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  object-fit: cover;
  opacity: 0;
  transition: var(--transition) ease-in-out;
}

.weather .weather-bg__img.bg {
  opacity: 1;
}

.weather.sunny .weather-bg__img:not(.sunny) {
  opacity: 0;
}

.weather.sunny .weather-bg__img.sunny {
  opacity: 1;
}

.weather.partly-cloudy .weather-bg__img:not(.partly-cloudy) {
  opacity: 0;
}

.weather.partly-cloudy .weather-bg__img.partly-cloudy {
  opacity: 1;
}

.weather.overcast .weather-bg__img:not(.overcast) {
  opacity: 0;
}

.weather.overcast .weather-bg__img.overcast {
  opacity: 1;
}

.weather.сloudy .weather-bg__img:not(.сloudy) {
  opacity: 0;
}

.weather.сloudy .weather-bg__img.сloudy {
  opacity: 1;
}

.weather-form,
.weather-load,
.weather-info {
  grid-column: 1 / 4;
}

.weather-load {
  display: flex;
  align-items: center;
  height: 87px;
}

.weather-info__text {
  display: grid;
  grid-template-columns: 1fr auto auto;
  gap: 20px;
  font-size: 40px;
}
</style>
