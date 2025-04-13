<script setup lang="ts">
import MoleHole from "./MoleHole.vue";

defineProps<{
  holes: boolean[];
  gameActive: boolean;
  currentAnimal: string;
  isGoldenMole: boolean;
}>();

defineEmits<{
  (e: "whack", index: number): void;
}>();
</script>

<template>
  <div class="game-board" :class="{ 'game-active': gameActive }">
    <div class="grid-overlay"></div>
    <div class="noise-overlay"></div>
    <MoleHole
      v-for="(hole, index) in holes"
      :key="index"
      :is-active="hole"
      :current-animal="currentAnimal"
      :is-golden="isGoldenMole && hole"
      :index="index"
      @whack="$emit('whack', index)"
    />
  </div>
</template>

<style scoped>
.game-board {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: clamp(0.5rem, 2vw, 1rem);
  aspect-ratio: 1;
  width: min(100%, 500px);
  margin: 0 auto;
  padding: clamp(0.5rem, 2vw, 1rem);
  background-color: var(--hole-color);
  border: 4px solid var(--text-color);
  position: relative;
  isolation: isolate;
  transform-style: preserve-3d;
  perspective: 1000px;
}

.grid-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(90deg, transparent 95%, var(--accent-color) 95%),
    linear-gradient(transparent 95%, var(--accent-color) 95%);
  background-size: 20px 20px;
  pointer-events: none;
  opacity: 0.1;
  z-index: 1;
}

.noise-overlay {
  position: absolute;
  inset: 0;
  background-image: url('data:image/svg+xml,<svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg"><filter id="noise"><feTurbulence type="fractalNoise" baseFrequency="0.65" numOctaves="3" stitchTiles="stitch"/></filter><rect width="100%" height="100%" filter="url(%23noise)" opacity="0.1"/></svg>');
  pointer-events: none;
  z-index: 2;
  mix-blend-mode: overlay;
}

.game-board::before {
  content: "";
  position: absolute;
  inset: -2px;
  background: repeating-linear-gradient(
    45deg,
    transparent,
    transparent 10px,
    rgba(255, 0, 0, 0.1) 10px,
    rgba(255, 0, 0, 0.1) 20px
  );
  z-index: -1;
}

.game-board::after {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(
    45deg,
    transparent 0%,
    rgba(255, 0, 0, 0.2) 50%,
    transparent 100%
  );
  pointer-events: none;
  animation: boardGlow 4s infinite;
}

.game-board.game-active {
  animation: boardPulse 2s infinite, boardGlitch 4s infinite;
  box-shadow: 0 0 20px rgba(255, 0, 0, 0.3), 0 0 40px rgba(255, 0, 0, 0.2),
    0 0 60px rgba(255, 0, 0, 0.1);
}

.mole-graphic,
.hole-graphic {
  font-size: clamp(2rem, 8vw, 3.5rem);
}

@keyframes boardPulse {
  0%,
  100% {
    transform: scale(1) rotateX(0deg);
  }
  50% {
    transform: scale(1.005) rotateX(0.5deg);
  }
}

@keyframes boardGlow {
  0%,
  100% {
    opacity: 0.5;
  }
  50% {
    opacity: 0.8;
  }
}

@keyframes boardGlitch {
  0%,
  100% {
    clip-path: inset(0 0 0 0);
  }
  2% {
    clip-path: inset(20% 0 0 0);
  }
  4% {
    clip-path: inset(0 0 20% 0);
  }
  6% {
    clip-path: inset(0 20% 0 0);
  }
  8% {
    clip-path: inset(0 0 0 20%);
  }
  10% {
    clip-path: inset(0 0 0 0);
  }
}

@media (max-width: 768px) {
  .game-board {
    width: 100%;
    border-width: 3px;
  }

  .mole-graphic,
  .hole-graphic {
    font-size: clamp(1.8rem, 7vw, 2.5rem);
  }
}
</style>
