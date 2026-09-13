<template>
  <div class="weather-dashboard">
    <div class="weather-header">
      <h3>🌤️ Weather Info</h3>
    </div>

    <div class="weather-location">
      <label for="location">Location:</label>
      <select
        id="location"
        @change="handleLocationChange"
      >
        <option 
          v-for="(coords, city) in locations" 
          :key="city"
          :value="city"
        >
          {{ city }}
        </option>
      </select>
    </div>

    <div v-if="weather" class="weather-info">
      <div class="weather-main">
        <div class="weather-icon">
          {{ getWeatherIcon(weather.weather_code) }}
        </div>
        <div class="temperature">
          <span class="temp-value">
            {{ Math.round(weather.temperature_2m) }}°C
          </span>
        </div>
      </div>

      <div class="weather-details">
        <div class="weather-item">
          <span class="icon">💧</span>
          <div class="detail">
            <span class="label">Humidity</span>
            <span class="value">{{ weather.relative_humidity_2m }}%</span>
          </div>
        </div>

        <div class="weather-item">
          <span class="icon">💨</span>
          <div class="detail">
            <span class="label">Wind Speed</span>
            <span class="value">{{ Math.round(weather.wind_speed_10m) }} km/h</span>
          </div>
        </div>

        <div class="weather-item">
          <span class="icon">🌧️</span>
          <div class="detail">
            <span class="label">Precipitation</span>
            <span class="value">{{ weather.precipitation }} mm</span>
          </div>
        </div>
      </div>

      <div class="weather-note">
        <p>⚠️ Check weather conditions before training!</p>
      </div>
    </div>
    <div v-else class="weather-loading">
      <p>Loading weather data...</p>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  name: 'WeatherDashboard',
  props: {
    weather: {
      type: Object,
      default: null
    }
  },
  emits: ['change-location'],
  setup(props, { emit }) {
    const selectedLocation = ref('Helsinki')
    const locations = ref({
      'Helsinki': { lat: 60.1699, lon: 24.9384 },
      'New York': { lat: 40.7128, lon: -74.0060 },
      'London': { lat: 51.5074, lon: -0.1278 },
      'Paris': { lat: 48.8566, lon: 2.3522 },
      'Barcelona': { lat: 41.3874, lon: 2.1686 },
    })

    const handleLocationChange = (e) => {
      const newLocation = e.target.value
      selectedLocation.value = newLocation
      const coords = locations.value[newLocation]
      emit('change-location', coords.lat, coords.lon)
    }

    const getWeatherIcon = (code) => {
      if (code === 0) return '☀️'
      if (code === 1 || code === 2) return '🌤️'
      if (code === 3) return '☁️'
      if (code === 45 || code === 48) return '🌫️'
      if (code >= 51 && code <= 67) return '🌧️'
      if (code >= 71 && code <= 77) return '❄️'
      if (code >= 80 && code <= 82) return '🌦️'
      if (code >= 85 && code <= 86) return '🌨️'
      if (code >= 80 && code <= 82) return '⛈️'
      return '🌡️'
    }

    return {
      selectedLocation,
      locations,
      handleLocationChange,
      getWeatherIcon
    }
  }
}
</script>

<style scoped>
.weather-dashboard {
  background: white;
  border-radius: 10px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.weather-header {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 15px;
  border-bottom: 3px solid rgba(255, 255, 255, 0.2);
}

.weather-header h3 {
  margin: 0;
  font-size: 1.1rem;
}

.weather-location {
  padding: 15px;
  border-bottom: 1px solid #eee;
}

.weather-location label {
  display: block;
  font-size: 0.9rem;
  color: #666;
  margin-bottom: 8px;
  font-weight: 500;
}

.weather-location select {
  width: 100%;
  padding: 8px;
  border: 2px solid #ddd;
  border-radius: 6px;
  font-size: 0.9rem;
  cursor: pointer;
  transition: border-color 0.3s ease;
}

.weather-location select:hover,
.weather-location select:focus {
  border-color: #667eea;
  outline: none;
}

.weather-info {
  padding: 15px;
  flex: 1;
}

.weather-main {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
  margin-bottom: 20px;
  padding: 15px;
  background: linear-gradient(135deg, #f5f7ff 0%, #f0e6ff 100%);
  border-radius: 8px;
}

.weather-icon {
  font-size: 3rem;
}

.temperature {
  text-align: center;
}

.temp-value {
  font-size: 2rem;
  font-weight: bold;
  color: #667eea;
}

.weather-details {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.weather-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px;
  background: #f9f9f9;
  border-radius: 6px;
  border-left: 4px solid #667eea;
}

.weather-item .icon {
  font-size: 1.5rem;
}

.weather-item .detail {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.weather-item .label {
  font-size: 0.75rem;
  color: #999;
  text-transform: uppercase;
  font-weight: 600;
}

.weather-item .value {
  font-size: 0.95rem;
  font-weight: bold;
  color: #333;
}

.weather-note {
  margin-top: 15px;
  padding: 12px;
  background: #fff3cd;
  border-left: 4px solid #ffc107;
  border-radius: 6px;
}

.weather-note p {
  margin: 0;
  color: #856404;
  font-size: 0.85rem;
  font-weight: 500;
}

.weather-loading {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 150px;
  color: #999;
}

@media (max-width: 768px) {
  .weather-main {
    flex-direction: column;
    gap: 10px;
  }

  .weather-icon {
    font-size: 2.5rem;
  }

  .temp-value {
    font-size: 1.5rem;
  }
}
</style>
