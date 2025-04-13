<script setup lang="ts">
defineProps<{
  score: number;
  combo: number;
  timeLeft: number;
}>();
</script>

<template>
  <div class="game-info">
    <div class="score-container">
      <div class="score">SCORE: {{ score }}</div>
      <div class="score-background"></div>
    </div>
    <div class="combo" v-if="combo > 1">
      <span class="combo-text">COMBO</span>
      <span class="combo-multiplier">x{{ combo }}!</span>
    </div>
    <div class="time-container">
      <div class="time" :class="{ 'time-low': timeLeft <= 10 }">
        TIME: {{ timeLeft }}s
      </div>
      <div class="time-background"></div>
    </div>
  </div>
</template>

<style scoped>
.game-info {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  width: 100%;
  font-size: clamp(1.2rem, 3vw, 1.8rem);
  font-weight: bold;
  text-transform: uppercase;
  position: relative;
}

.score-container,
.time-container {
  position: relative;
  overflow: hidden;
  isolation: isolate;
  clip-path: polygon(
    0 10%,
    3% 0,
    97% 0,
    100% 10%,
    100% 90%,
    97% 100%,
    3% 100%,
    0 90%
  );
}

.score,
.time {
  border: 3px solid var(--text-color);
  padding: 0.5em;
  background: var(--main-bg);
  position: relative;
  z-index: 1;
}

.score-background,
.time-background {
  position: absolute;
  inset: 0;
  background: repeating-linear-gradient(
    45deg,
    transparent,
    transparent 10px,
    rgba(255, 0, 0, 0.1) 10px,
    rgba(255, 0, 0, 0.1) 20px
  );
  z-index: 0;
  animation: backgroundSlide 20s linear infinite;
}

.time-low {
  color: var(--accent-color);
  animation: warning 0.5s infinite;
  border-color: var(--accent-color);
  text-shadow: 0 0 10px var(--accent-color);
}

.time-low::before {
  content: "⚠️";
  position: absolute;
  right: 0.5em;
  animation: warningBlink 0.5s infinite;
}

.combo {
  position: absolute;
  top: -1.5em;
  left: 50%;
  transform: translateX(-50%);
  color: var(--accent-color);
  font-size: clamp(1.8rem, 4vw, 2.2rem);
  text-shadow: 3px 3px 0 var(--text-color), -3px -3px 0 var(--text-color),
    3px -3px 0 var(--text-color), -3px 3px 0 var(--text-color),
    0 0 20px var(--accent-color);
  animation: comboPopup 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  white-space: nowrap;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.2em;
}

.combo-text {
  font-size: 0.5em;
  letter-spacing: 0.2em;
}

.combo-multiplier {
  font-size: 1.2em;
  animation: comboPulse 0.5s infinite;
}

@keyframes warning {
  0%,
  100% {
    transform: scale(1);
    filter: brightness(1);
  }
  50% {
    transform: scale(1.05);
    filter: brightness(1.2);
    background-color: rgba(255, 0, 0, 0.2);
  }
}

@keyframes warningBlink {
  0%,
  100% {
    opacity: 1;
    transform: scale(1);
  }
  50% {
    opacity: 0.5;
    transform: scale(1.2);
  }
}

@keyframes comboPopup {
  0% {
    transform: translateX(-50%) scale(0) rotate(-10deg);
    filter: brightness(0.5);
  }
  50% {
    transform: translateX(-50%) scale(1.2) rotate(5deg);
    filter: brightness(1.2);
  }
  100% {
    transform: translateX(-50%) scale(1) rotate(0);
    filter: brightness(1);
  }
}

@keyframes comboPulse {
  0%,
  100% {
    transform: scale(1);
    filter: brightness(1);
  }
  50% {
    transform: scale(1.1);
    filter: brightness(1.3);
  }
}

@keyframes backgroundSlide {
  0% {
    background-position: 0 0;
  }
  100% {
    background-position: 100px 100px;
  }
}

@media (max-width: 768px) {
  .game-info {
    gap: 0.5rem;
    font-size: clamp(1rem, 2.5vw, 1.5rem);
  }

  .score,
  .time {
    border-width: 2px;
    padding: 0.3em;
  }

  .combo {
    font-size: clamp(1.5rem, 3vw, 2rem);
    top: -1em;
  }
}
</style>
