<script setup lang="ts">
defineProps<{
  isActive: boolean;
}>();
</script>

<template>
  <div v-if="isActive" class="power-up-indicator">
    <div class="power-text">POWER UP!</div>
    <div class="multiplier">x2</div>
    <div class="points">POINTS</div>
  </div>
</template>

<style scoped>
.power-up-indicator {
  color: var(--power-up-color);
  font-size: clamp(1.2rem, 4vw, 1.8rem);
  font-weight: bold;
  text-transform: uppercase;
  padding: 1em;
  position: relative;
  text-shadow: -2px -2px 0 var(--accent-color), 2px -2px 0 var(--accent-color),
    -2px 2px 0 var(--accent-color), 2px 2px 0 var(--accent-color),
    0 0 20px var(--power-up-color);
  animation: powerUpFloat 1s infinite ease-in-out;
  width: 100%;
  background: repeating-linear-gradient(
      -45deg,
      transparent,
      transparent 10px,
      rgba(255, 255, 0, 0.1) 10px,
      rgba(255, 255, 0, 0.1) 20px
    ),
    linear-gradient(to right, rgba(255, 255, 0, 0.1), rgba(255, 0, 0, 0.1));
  border: 4px solid var(--power-up-color);
  box-shadow: 0 0 20px rgba(255, 255, 0, 0.3),
    inset 0 0 20px rgba(255, 255, 0, 0.3);
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
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.2em;
}

.power-text {
  font-size: 1.2em;
  animation: glitch 0.3s infinite;
}

.multiplier {
  font-size: 2em;
  color: var(--accent-color);
  text-shadow: -2px -2px 0 var(--power-up-color),
    2px -2px 0 var(--power-up-color), -2px 2px 0 var(--power-up-color),
    2px 2px 0 var(--power-up-color);
  animation: pulse 0.5s infinite;
}

.points {
  font-size: 0.8em;
}

@keyframes powerUpFloat {
  0%,
  100% {
    transform: translateY(0) scale(1);
    filter: brightness(1) contrast(1);
  }
  50% {
    transform: translateY(-5px) scale(1.02);
    filter: brightness(1.2) contrast(1.1);
  }
}

@keyframes glitch {
  0% {
    transform: translate(0);
  }
  20% {
    transform: translate(-2px, 2px);
  }
  40% {
    transform: translate(-2px, -2px);
  }
  60% {
    transform: translate(2px, 2px);
  }
  80% {
    transform: translate(2px, -2px);
  }
  100% {
    transform: translate(0);
  }
}

@keyframes pulse {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
}

@media (max-width: 768px) {
  .power-up-indicator {
    padding: 0.5em;
    border-width: 2px;
    font-size: clamp(1rem, 3vw, 1.5rem);
  }
}
</style>
