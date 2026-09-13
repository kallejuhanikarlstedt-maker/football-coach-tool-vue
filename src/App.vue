<template>
  <div class="app">
    <header class="app-header">
      <h1>⚽ Football Coach Tool</h1>
      <p>Drag and drop players to arrange your formation</p>
    </header>

    <div class="app-container">
      <div class="main-content">
        <FieldLayout 
          :players="players" 
          @update-player="handlePlayerDrag"
        />
      </div>
      <div class="sidebar">
        <WeatherDashboard 
          :weather="weather"
          @change-location="handleLocationChange"
        />
        <div class="players-list">
          <h3>Team Squad</h3>
          <ul>
            <li v-for="player in players" :key="player.id">
              <strong>{{ player.id }}.</strong> {{ player.name }}
              <span class="position">{{ player.position }}</span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import FieldLayout from './components/FieldLayout.vue'
import WeatherDashboard from './components/WeatherDashboard.vue'

export default {
  name: 'App',
  components: {
    FieldLayout,
    WeatherDashboard
  },
  setup() {
    const players = ref([
      { id: 1, name: 'Goalkeeper', position: 'GK', x: 50, y: 10 },
      { id: 2, name: 'Defender 1', position: 'CB', x: 30, y: 30 },
      { id: 3, name: 'Defender 2', position: 'CB', x: 70, y: 30 },
      { id: 4, name: 'Left Back', position: 'LB', x: 20, y: 50 },
      { id: 5, name: 'Right Back', position: 'RB', x: 80, y: 50 },
      { id: 6, name: 'Midfielder 1', position: 'CM', x: 40, y: 60 },
      { id: 7, name: 'Midfielder 2', position: 'CM', x: 60, y: 60 },
      { id: 8, name: 'Left Wing', position: 'LW', x: 25, y: 75 },
      { id: 9, name: 'Right Wing', position: 'RW', x: 75, y: 75 },
      { id: 10, name: 'Striker', position: 'ST', x: 50, y: 90 },
    ])

    const weather = ref(null)
    const location = ref({ lat: 60.1699, lon: 24.9384 })

    onMounted(() => {
      fetchWeather(location.value.lat, location.value.lon)
    })

    const fetchWeather = async (lat, lon) => {
      try {
        const response = await fetch(
          `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current=temperature_2m,relative_humidity_2m,weather_code,wind_speed_10m,precipitation&timezone=auto`
        )
        const data = await response.json()
        weather.value = data.current
      } catch (error) {
        console.error('Error fetching weather:', error)
      }
    }

    const handlePlayerDrag = (id, newX, newY) => {
      const player = players.value.find(p => p.id === id)
      if (player) {
        player.x = newX
        player.y = newY
      }
    }

    const handleLocationChange = (lat, lon) => {
      location.value = { lat, lon }
      fetchWeather(lat, lon)
    }

    return {
      players,
      weather,
      handlePlayerDrag,
      handleLocationChange
    }
  }
}
</script>

<style scoped>
.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.app-header {
  text-align: center;
  color: white;
  padding: 30px 20px;
  background: rgba(0, 0, 0, 0.2);
  border-bottom: 3px solid rgba(255, 255, 255, 0.3);
  margin-bottom: 20px;
}

.app-header h1 {
  font-size: 2.5rem;
  margin-bottom: 10px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

.app-header p {
  font-size: 1.1rem;
  opacity: 0.9;
}

.app-container {
  display: flex;
  gap: 20px;
  flex: 1;
  max-width: 1400px;
  margin: 0 auto;
  width: 100%;
}

.main-content {
  flex: 1;
  min-width: 0;
}

.sidebar {
  width: 300px;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.players-list {
  background: white;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  max-height: 500px;
  overflow-y: auto;
}

.players-list h3 {
  margin-bottom: 15px;
  color: #333;
  border-bottom: 2px solid #667eea;
  padding-bottom: 10px;
}

.players-list ul {
  list-style: none;
}

.players-list li {
  padding: 8px 0;
  border-bottom: 1px solid #eee;
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 0.9rem;
}

.players-list li:last-child {
  border-bottom: none;
}

.players-list strong {
  color: #667eea;
  margin-right: 8px;
}

.position {
  background: #667eea;
  color: white;
  padding: 2px 8px;
  border-radius: 12px;
  font-size: 0.75rem;
  font-weight: bold;
}

@media (max-width: 1024px) {
  .app-container {
    flex-direction: column;
  }

  .sidebar {
    width: 100%;
    flex-direction: row;
  }

  .app-header h1 {
    font-size: 2rem;
  }
}
</style>
