<script setup lang="ts">
defineProps<{
  gameActive: boolean;
}>();

defineEmits<{
  (e: "start"): void;
}>();
</script>

<template>
  <button
    @click="$emit('start')"
    :disabled="gameActive"
    class="start-button"
    :class="{ active: gameActive }"
  >
    <div
      class="glitch-text"
      :data-text="gameActive ? 'GAME IN PROGRESS' : 'START GAME'"
    >
      {{ gameActive ? "GAME IN PROGRESS" : "START GAME" }}
    </div>
    <div class="button-background"></div>
    <div class="button-border"></div>
  </button>
</template>

<style scoped>
.start-button {
  font-size: clamp(1.2rem, 4vw, 1.8rem);
  padding: 0.8em 1.5em;
  background-color: var(--main-bg);
  color: var(--text-color);
  border: 4px solid var(--text-color);
  cursor: pointer;
  font-family: "Courier New", monospace;
  font-weight: bold;
  text-transform: uppercase;
  position: relative;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  margin-top: 2rem;
  width: 100%;
  max-width: 400px;
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
  overflow: hidden;
  transform-style: preserve-3d;
  perspective: 1000px;
}

.glitch-text {
  position: relative;
  z-index: 2;
  mix-blend-mode: difference;
}

.glitch-text::before,
.glitch-text::after {
  content: attr(data-text);
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0.8;
}

.glitch-text::before {
  color: var(--accent-color);
  animation: glitch-1 2s infinite linear alternate-reverse;
}

.glitch-text::after {
  color: var(--glitch-color);
  animation: glitch-2 3s infinite linear alternate-reverse;
}

.button-background {
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
  transform: translateZ(-10px);
  animation: backgroundMove 20s linear infinite;
}

.button-border {
  position: absolute;
  inset: -2px;
  background: linear-gradient(
    45deg,
    var(--accent-color),
    var(--text-color),
    var(--accent-color)
  );
  z-index: -1;
  animation: borderRotate 4s linear infinite;
}

.start-button:not(:disabled):hover {
  transform: translate(-4px, -4px) scale(1.02);
  box-shadow: 4px 4px 0 var(--text-color), 8px 8px 0 var(--accent-color),
    12px 12px 20px rgba(255, 0, 0, 0.3);
}

.start-button:not(:disabled):hover .glitch-text::before {
  animation: glitch-1 0.5s infinite linear alternate-reverse;
}

.start-button:not(:disabled):hover .glitch-text::after {
  animation: glitch-2 0.5s infinite linear alternate-reverse;
}

.start-button:not(:disabled):active {
  transform: translate(2px, 2px) scale(0.98);
  box-shadow: none;
}

.start-button:disabled {
  opacity: 0.7;
  cursor: not-allowed;
  border-color: var(--accent-color);
  animation: disabledPulse 2s infinite;
}

@keyframes glitch-1 {
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

@keyframes glitch-2 {
  0% {
    transform: translate(0);
  }
  20% {
    transform: translate(2px, -2px);
  }
  40% {
    transform: translate(2px, 2px);
  }
  60% {
    transform: translate(-2px, -2px);
  }
  80% {
    transform: translate(-2px, 2px);
  }
  100% {
    transform: translate(0);
  }
}

@keyframes backgroundMove {
  0% {
    background-position: 0 0;
  }
  100% {
    background-position: 500px 500px;
  }
}

@keyframes borderRotate {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes disabledPulse {
  0%,
  100% {
    opacity: 0.7;
    transform: scale(1);
  }
  50% {
    opacity: 0.5;
    transform: scale(0.99);
  }
}

@media (max-width: 768px) {
  .start-button {
    border-width: 3px;
    margin-top: 1rem;
    font-size: clamp(1rem, 3vw, 1.5rem);
  }
}
</style>
