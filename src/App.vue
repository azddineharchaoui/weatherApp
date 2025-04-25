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
    shortForecast: 'Hourly Forcasts ',
    useLocation: 'Use My Location'
  },
  fr: {
    title: 'Application Météo',
    enterCity: 'Entrez une ville',
    search: 'Rechercher',
    temp: 'Température',
    humidity: 'Humidité',
    wind: 'Vitesse du vent',
    shortForecast: 'Prévisions par heure',
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
  font-family: 'Quicksand', 'Segoe UI', Tahoma, sans-serif;
  max-width: 500px;
  margin: 2rem auto;
  padding: 1.5rem;
  border-radius: 20px;
  background: linear-gradient(to bottom right, rgba(232, 245, 254, 0.85), rgba(240, 249, 255, 0.9));
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
  color: #3a5070;
  transition: all 0.3s ease;
  backdrop-filter: blur(5px);
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.header h1 {
  font-size: 2rem;
  color: #2980b9;
  margin: 0;
}

.header select {
  padding: 0.5rem 0.8rem;
  border-radius: 15px;
  border: 1px solid #d8e6f2;
  background-color: white;
  color: #3a5070;
  font-family: inherit;
  transition: all 0.2s ease;
  cursor: pointer;
  outline: none;
}

.header select:hover {
  border-color: #7aadcc;
}

.search-section {
  display: flex;
  gap: 0.8rem;
  margin: 1.2rem 0;
  flex-wrap: wrap;
}

input {
  flex: 1;
  padding: 0.8rem 1rem;
  border: 1px solid #d8e6f2;
  border-radius: 15px;
  font-family: inherit;
  color: #3a5070;
  font-size: 1rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.2s ease;
}

input:focus {
  outline: none;
  border-color: #7aadcc;
  box-shadow: 0 2px 10px rgba(41, 128, 185, 0.15);
}

button {
  padding: 0.8rem 1.2rem;
  border: none;
  background: linear-gradient(135deg, #3498db, #2980b9);
  color: white;
  border-radius: 15px;
  cursor: pointer;
  font-family: inherit;
  font-weight: 600;
  font-size: 0.95rem;
  transition: all 0.2s ease;
  box-shadow: 0 3px 8px rgba(41, 128, 185, 0.2);
}

button:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 12px rgba(41, 128, 185, 0.3);
}

button:active {
  transform: translateY(0);
}

.current-weather, .forecast {
  margin-top: 1.5rem;
  text-align: center;
  background-color: rgba(255, 255, 255, 0.65);
  border-radius: 18px;
  padding: 1.2rem;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.04);
}

.current-weather h2 {
  color: #2980b9;
  margin-top: 0;
}

.current-weather img {
  width: 90px;
  height: 90px;
  margin: 0.5rem 0;
  filter: drop-shadow(0 3px 5px rgba(0, 0, 0, 0.1));
}

.current-weather p {
  margin: 0.7rem 0;
  font-size: 1.1rem;
}

.forecast h3 {
  color: #2980b9;
  margin-top: 0;
  margin-bottom: 1rem;
}

.forecast-items {
  display: flex;
  overflow-x: auto;
  scroll-behavior: smooth;
  gap: 12px;
  padding: 10px 0;
  margin-top: 0.5rem;
  /* Hide scrollbar for Chrome, Safari and Opera */
  &::-webkit-scrollbar {
    display: none;
  }
  /* Hide scrollbar for IE, Edge and Firefox */
  -ms-overflow-style: none;  /* IE and Edge */
  scrollbar-width: none;  /* Firefox */
}

.forecast-item {
  background: white;
  padding: 0.8rem;
  border-radius: 15px;
  min-width: 110px;
  flex-shrink: 0;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.05);
  transition: all 0.2s ease;
}

.forecast-item:hover {
  transform: translateY(-3px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
}

.forecast-item p {
  margin: 0.5rem 0;
  font-weight: 500;
}

.forecast-item img {
  width: 60px;
  height: 60px;
  filter: drop-shadow(0 3px 3px rgba(0, 0, 0, 0.1));
}
</style>
