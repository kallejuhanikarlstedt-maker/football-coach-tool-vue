<template>
  <div
    class="player"
    :class="{ dragging: isDragging }"
    :style="{
      left: `${player.x}%`,
      top: `${player.y}%`,
    }"
    @mousedown="$emit('mousedown', $event)"
  >
    <div class="player-circle">
      <span class="player-number">{{ player.id }}</span>
    </div>
    <div class="player-tooltip">
      {{ player.name }}
      <br />
      <small>{{ player.position }}</small>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PlayerCard',
  props: {
    player: {
      type: Object,
      required: true
    },
    isDragging: {
      type: Boolean,
      default: false
    }
  },
  emits: ['mousedown']
}
</script>

<style scoped>
.player {
  position: absolute;
  transform: translate(-50%, -50%);
  cursor: grab;
  transition: transform 0.1s ease-out;
}

.player.dragging {
  cursor: grabbing;
  transform: translate(-50%, -50%) scale(1.1);
  z-index: 1000;
}

.player-circle {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  border: 3px solid white;
  transition: all 0.2s ease;
}

.player:hover .player-circle {
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.4);
  transform: scale(1.05);
}

.player.dragging .player-circle {
  transform: scale(1);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.5);
}

.player-number {
  color: white;
  font-weight: bold;
  font-size: 1.5rem;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
}

.player-tooltip {
  position: absolute;
  bottom: 100%;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.9);
  color: white;
  padding: 8px 12px;
  border-radius: 6px;
  white-space: nowrap;
  font-size: 0.9rem;
  margin-bottom: 10px;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.2s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
}

.player-tooltip small {
  display: block;
  font-size: 0.75rem;
  opacity: 0.8;
  margin-top: 2px;
}

.player:hover .player-tooltip {
  opacity: 1;
}

@media (max-width: 768px) {
  .player-circle {
    width: 40px;
    height: 40px;
  }

  .player-number {
    font-size: 1.2rem;
  }
}
</style>
