<template>
  <div
    class="field-container"
    ref="fieldRef"
    @mousemove="handleMouseMove"
    @mouseup="handleMouseUp"
    @mouseleave="handleMouseUp"
  >
    <div class="field">
      <!-- Field lines -->
      <div class="field-line horizontal center-line"></div>
      <div class="field-line vertical"></div>

      <!-- Goal areas -->
      <div class="goal-area goal-area-top">
        <div class="penalty-area">
          <div class="goal-box"></div>
        </div>
      </div>
      <div class="goal-area goal-area-bottom">
        <div class="penalty-area">
          <div class="goal-box"></div>
        </div>
      </div>

      <!-- Center circle -->
      <div class="center-circle"></div>

      <!-- Players -->
      <PlayerCard
        v-for="player in players"
        :key="player.id"
        :player="player"
        :isDragging="draggingId === player.id"
        @mousedown="handleMouseDown($event, player.id)"
      />
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'
import PlayerCard from './PlayerCard.vue'

export default {
  name: 'FieldLayout',
  components: {
    PlayerCard
  },
  props: {
    players: {
      type: Array,
      required: true
    }
  },
  emits: ['update-player'],
  setup(props, { emit }) {
    const fieldRef = ref(null)
    const draggingId = ref(null)
    const offset = ref({ x: 0, y: 0 })

    const handleMouseDown = (e, playerId) => {
      if (!fieldRef.value) return

      draggingId.value = playerId
      const rect = fieldRef.value.getBoundingClientRect()
      const player = props.players.find(p => p.id === playerId)

      offset.value = {
        x: e.clientX - rect.left - (player.x * rect.width) / 100,
        y: e.clientY - rect.top - (player.y * rect.height) / 100,
      }
    }

    const handleMouseMove = (e) => {
      if (draggingId.value === null || !fieldRef.value) return

      const rect = fieldRef.value.getBoundingClientRect()
      let newX = ((e.clientX - rect.left - offset.value.x) / rect.width) * 100
      let newY = ((e.clientY - rect.top - offset.value.y) / rect.height) * 100

      newX = Math.max(0, Math.min(100, newX))
      newY = Math.max(0, Math.min(100, newY))

      emit('update-player', draggingId.value, newX, newY)
    }

    const handleMouseUp = () => {
      draggingId.value = null
    }

    return {
      fieldRef,
      draggingId,
      handleMouseDown,
      handleMouseMove,
      handleMouseUp
    }
  }
}
</script>

<style scoped>
.field-container {
  width: 100%;
  height: 600px;
  background: white;
  border-radius: 10px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  user-select: none;
}

.field {
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #2d5016 0%, #3d6b1f 50%, #2d5016 100%);
  position: relative;
  cursor: grab;
}

.field:active {
  cursor: grabbing;
}

/* Field lines */
.field-line {
  position: absolute;
  background: rgba(255, 255, 255, 0.8);
}

.field-line.horizontal {
  width: 100%;
  height: 2px;
  top: 50%;
}

.field-line.vertical {
  width: 2px;
  height: 100%;
  left: 50%;
}

.center-line {
  border-top: 2px dashed rgba(255, 255, 255, 0.6);
}

/* Goal areas */
.goal-area {
  position: absolute;
  width: 100%;
  height: 25%;
  left: 0;
  border: 2px solid rgba(255, 255, 255, 0.6);
}

.goal-area-top {
  top: 0;
}

.goal-area-bottom {
  bottom: 0;
}

.penalty-area {
  width: 100%;
  height: 100%;
  position: relative;
  border: 2px solid rgba(255, 255, 255, 0.6);
}

.goal-box {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  width: 40%;
  height: 40%;
  border: 2px solid rgba(255, 255, 255, 0.6);
  top: 50%;
}

.goal-area-bottom .goal-box {
  bottom: 50%;
  top: auto;
}

/* Center circle */
.center-circle {
  position: absolute;
  width: 150px;
  height: 150px;
  border: 2px solid rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}

.center-circle::after {
  content: '';
  position: absolute;
  width: 8px;
  height: 8px;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}

@media (max-width: 768px) {
  .field-container {
    height: 400px;
  }
}
</style>
