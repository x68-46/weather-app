<template>
  <div class="weather-ios-container">
    <div class="weather-center-wrap">
      <div class="weather-card" :class="weatherTheme">
        <form @submit.prevent="getWeather" class="search-bar">
          <input v-model="city" placeholder="Search city" required />
          <button type="submit">
            <svg width="20" height="20" fill="none" viewBox="0 0 24 24"><circle cx="11" cy="11" r="8" stroke="#4f8ef7" stroke-width="2"/><path d="M21 21l-3.5-3.5" stroke="#4f8ef7" stroke-width="2" stroke-linecap="round"/></svg>
          </button>
        </form>
        <div v-if="loading" class="loading">Loading...</div>
        <div v-if="error" class="error">{{ error }}</div>
        <div v-if="weather" class="weather-info">
          <div class="main-info">
            <img :src="getIconUrl(weather.weather[0].icon)" :alt="weather.weather[0].description" class="weather-icon" />
            <div class="temp">{{ Math.round(weather.main.temp) }}°</div>
          </div>
          <div class="desc">{{ capitalize(weather.weather[0].description) }}</div>
          <div class="city">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="details">
            <div>
              <span>Humidity</span>
              <span>{{ weather.main.humidity }}%</span>
            </div>
            <div>
              <span>Wind</span>
              <span>{{ weather.wind.speed }} m/s</span>
            </div>
          </div>
        </div>
      </div>
      <div class="footer">Powered by OpenWeatherMap</div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue';
import axios from 'axios';

const city = ref('');
const weather = ref(null);
const loading = ref(false);
const error = ref('');

const API_KEY = import.meta.env.VITE_OPENWEATHERMAP_API_KEY;

function getIconUrl(icon) {
  return `https://openweathermap.org/img/wn/${icon}@4x.png`;
}

function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

const weatherTheme = computed(() => {
  if (!weather.value) return 'theme-default';
  const main = weather.value.weather[0].main.toLowerCase();
  if (main.includes('clear')) return 'theme-sunny';
  if (main.includes('rain') || main.includes('drizzle') || main.includes('thunderstorm')) return 'theme-rainy';
  if (main.includes('cloud')) return 'theme-cloudy';
  if (main.includes('snow')) return 'theme-snowy';
  return 'theme-default';
});

// Dynamically update body class for background
watch(weatherTheme, (theme, oldTheme) => {
  document.body.classList.remove(oldTheme);
  document.body.classList.add(theme);
}, { immediate: true });

async function getWeather() {
  if (!city.value) return;
  loading.value = true;
  error.value = '';
  weather.value = null;
  try {
    const response = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather`, {
        params: {
          q: city.value,
          appid: API_KEY,
          units: 'metric',
        },
      }
    );
    weather.value = response.data;
  } catch (err) {
    error.value = 'City not found or API error.';
  } finally {
    loading.value = false;
  }
}
</script>

<style>
body.theme-sunny {
  background: linear-gradient(180deg, #4f8ef7 0%, #1e3c72 100%);
}
body.theme-rainy {
  background: linear-gradient(180deg, #232526 0%, #414345 100%);
}
body.theme-cloudy {
  background: linear-gradient(180deg, #d3d3d3 0%, #a7a7a7 100%);
}
body.theme-snowy {
  background: linear-gradient(180deg, #e0eafc 0%, #cfdef3 100%);
}
body.theme-default {
  background: linear-gradient(180deg, #4f8ef7 0%, #1e3c72 100%);
}
html, body, #app {
  width: 100%;
  min-height: 100vh;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  overflow-x: hidden;
}
.weather-center-wrap {
  min-height: 100vh;
  width: 100vw;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.weather-card {
  background: rgba(255,255,255,0.15);
  border-radius: 32px;
  box-shadow: 0 8px 32px 0 rgba(31,38,135,0.37);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border: 1px solid rgba(255,255,255,0.18);
  padding: 2.5rem 2rem 2rem 2rem;
  max-width: 400px;
  width: 90vw;
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: background 0.5s, color 0.5s;
}
.theme-sunny {
  background: rgba(76, 156, 255, 0.25) !important;
  color: #fff;
}
.theme-rainy {
  background: rgba(40, 40, 50, 0.7) !important;
  color: #e3e9f7;
}
.theme-cloudy {
  background: rgba(200, 200, 200, 0.5) !important;
  color: #222;
}
.theme-snowy {
  background: rgba(220, 240, 255, 0.7) !important;
  color: #222;
}
.theme-default {
  background: rgba(255,255,255,0.15) !important;
  color: #fff;
}
.theme-sunny.weather-ios-container {
  background: linear-gradient(180deg, #4f8ef7 0%, #1e3c72 100%);
}
.theme-rainy.weather-ios-container {
  background: linear-gradient(180deg, #232526 0%, #414345 100%);
}
.theme-cloudy.weather-ios-container {
  background: linear-gradient(180deg, #d3d3d3 0%, #a7a7a7 100%);
}
.theme-snowy.weather-ios-container {
  background: linear-gradient(180deg, #e0eafc 0%, #cfdef3 100%);
}
.theme-default.weather-ios-container {
  background: linear-gradient(180deg, #4f8ef7 0%, #1e3c72 100%);
}
.search-bar {
  display: flex;
  width: 100%;
  margin-bottom: 1.5rem;
}
.search-bar input {
  flex: 1;
  padding: 0.7rem 1rem;
  border-radius: 20px 0 0 20px;
  border: none;
  outline: none;
  font-size: 1rem;
  background: rgba(255,255,255,0.7);
}
.search-bar button {
  background: rgba(255,255,255,0.7);
  border: none;
  border-radius: 0 20px 20px 0;
  padding: 0 1rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  transition: background 0.2s;
}
.search-bar button:hover {
  background: #e3e9f7;
}
.loading {
  color: #4f8ef7;
  font-weight: 500;
  margin-bottom: 1rem;
}
.error {
  color: #ff4d4f;
  font-weight: 500;
  margin-bottom: 1rem;
}
.weather-info {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.main-info {
  display: flex;
  align-items: center;
  gap: 1.5rem;
}
.weather-icon {
  width: 90px;
  height: 90px;
  filter: drop-shadow(0 2px 8px rgba(0,0,0,0.15));
}
.temp {
  font-size: 4.5rem;
  font-weight: 700;
  color: inherit;
  text-shadow: 0 2px 8px rgba(0,0,0,0.15);
}
.desc {
  font-size: 1.3rem;
  color: inherit;
  margin-top: 0.5rem;
  text-shadow: 0 1px 4px rgba(0,0,0,0.10);
}
.city {
  font-size: 1.1rem;
  color: inherit;
  margin-bottom: 1.5rem;
  margin-top: 0.2rem;
}
.details {
  display: flex;
  gap: 2rem;
  color: inherit;
  font-size: 1rem;
  margin-top: 0.5rem;
  opacity: 0.9;
}
.details > div {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.details span:first-child {
  font-size: 0.9rem;
  color: #e3e9f7;
}
.footer {
  margin-top: 2rem;
  color: #222;
  font-size: 0.9rem;
  opacity: 0.95;
  letter-spacing: 1px;
  text-align: center;
  text-shadow: 0 1px 4px rgba(255,255,255,0.5), 0 1px 8px rgba(0,0,0,0.08);
}
@media (max-width: 400px) {
  .weather-card {
    min-width: 90vw;
    padding: 1.5rem 0.5rem 1.5rem 0.5rem;
  }
  .main-info {
    gap: 0.7rem;
  }
  .temp {
    font-size: 3rem;
  }
  .weather-icon {
    width: 60px;
    height: 60px;
  }
}
</style> 