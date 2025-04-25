<template>
  <div class="weather-app">
    <div class="header">
      <h1>{{ t('title') }}</h1>
      <select v-model="lang" @change="switchLanguage">
        <option value="en">English</option>
        <option value="fr">Français</option>
      </select>
    </div>

    <div class="search-section">
      <input v-model="city" @keyup.enter="fetchWeather" :placeholder="t('enterCity')" />
      <button @click="fetchWeather">{{ t('search') }}</button>
      <button @click="useGeolocation">{{ t('useLocation') }}</button>
    </div>

    <div v-if="weather" class="current-weather">
      <h2>{{ weather.location.name }}, {{ weather.location.country }}</h2>
      <img :src="getIconUrl(weather.current.condition.icon)" alt="weather icon" />
      <p>{{ weather.current.condition.text }}</p>
      <p>{{ t('temp') }}: {{ weather.current.temp_c }} °C</p>
      <p>{{ t('humidity') }}: {{ weather.current.humidity }}%</p>
      <p>{{ t('wind') }}: {{ weather.current.wind_kph }} km/h</p>
    </div>

    <div v-if="forecast.length" class="forecast">
      <h3>{{ t('shortForecast') }}</h3>
      <div class="forecast-items">
        <div v-for="(item, index) in forecast" :key="index" class="forecast-item">
          <p>{{ formatHour(item.time) }}</p>
          <img :src="getIconUrl(item.condition.icon)" />
          <p>{{ item.temp_c }} °C</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const API_KEY = '1d493fb85b524203af5111937251704'
const city = ref('')
const weather = ref(null)
const forecast = ref([])
const lang = ref('en')

const translations = {
  en: {
    title: 'Weather App',
    enterCity: 'Enter city',
    search: 'Search',
    temp: 'Temperature',
    humidity: 'Humidity',
    wind: 'Wind Speed',
    shortForecast: 'Next 3 hours',
    useLocation: 'Use My Location'
  },
  fr: {
    title: 'Application Météo',
    enterCity: 'Entrez une ville',
    search: 'Rechercher',
    temp: 'Température',
    humidity: 'Humidité',
    wind: 'Vitesse du vent',
    shortForecast: 'Prochaines 3 heures',
    useLocation: 'Utiliser ma position'
  }
}

function t(key) {
  return translations[lang.value][key] || key
}

function fetchWeather() {
  if (!city.value) return
  const url = `https://api.weatherapi.com/v1/forecast.json?key=${API_KEY}&q=${city.value}&lang=${lang.value}&hours=3`
  axios.get(url).then(res => {
    weather.value = res.data
    forecast.value = res.data.forecast.forecastday[0].hour
  })
}

function getIconUrl(iconCode) {
  return `https:${iconCode}`
}

function formatHour(dateString) {
  const date = new Date(dateString)
  return date.toLocaleTimeString(lang.value, { hour: '2-digit', minute: '2-digit' })
}

function useGeolocation() {
  navigator.geolocation.getCurrentPosition(pos => {
    const { latitude, longitude } = pos.coords
    const url = `https://api.weatherapi.com/v1/forecast.json?key=${API_KEY}&q=${latitude},${longitude}&lang=${lang.value}&hours=3`
    axios.get(url).then(res => {
      weather.value = res.data
      city.value = res.data.location.name
      forecast.value = res.data.forecast.forecastday[0].hour
    })
  })
}

function switchLanguage() {
  if (city.value) fetchWeather()
}
</script>

<style scoped>
.weather-app {
  font-family: sans-serif;
  max-width: 500px;
  margin: 2rem auto;
  padding: 1rem;
  border-radius: 12px;
  background: #f0f4f8;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.search-section {
  display: flex;
  gap: 0.5rem;
  margin: 1rem 0;
  flex-wrap: wrap;
}

input {
  flex: 1;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 6px;
}

button {
  padding: 0.5rem 1rem;
  border: none;
  background: #3498db;
  color: white;
  border-radius: 6px;
  cursor: pointer;
}

.current-weather, .forecast {
  margin-top: 1rem;
  text-align: center;
}

.forecast-items {
  display: flex;
  justify-content: space-around;
  margin-top: 0.5rem;
}

.forecast-item {
  background: white;
  padding: 0.5rem;
  border-radius: 8px;
  width: 30%;
}
</style>
