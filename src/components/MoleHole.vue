<script setup lang="ts">
defineProps<{
  isActive: boolean;
  currentAnimal: string;
  index: number;
  isGolden?: boolean;
}>();

defineEmits<{
  (e: "whack", index: number): void;
}>();
</script>

<template>
  <div
    class="hole"
    :class="{ mole: isActive, golden: isGolden }"
    @click="$emit('whack', index)"
  >
    <div class="hole-inner">
      <div class="depth-effect"></div>
      <div v-if="isActive" class="mole-graphic" :data-animal="currentAnimal">
        {{ currentAnimal }}
        <div class="hit-effect"></div>
      </div>
      <div v-else class="hole-graphic">⚫</div>
    </div>
  </div>
</template>

<style scoped>
.hole {
  aspect-ratio: 1;
  border: 3px solid var(--text-color);
  background: var(--hole-color);
  position: relative;
  cursor: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='40' height='48' viewport='0 0 100 100' style='fill:black;font-size:24px;'><text y='50%'>🔨</text></svg>")
      16 0,
    pointer;
  overflow: hidden;
  transition: all 0.1s cubic-bezier(0.22, 0.61, 0.36, 1);
  transform-style: preserve-3d;
  perspective: 1000px;
}

.hole-inner {
  position: relative;
  width: 100%;
  height: 100%;
  background: radial-gradient(
    circle at center,
    var(--hole-color) 0%,
    black 100%
  );
  transform-style: preserve-3d;
  transition: transform 0.3s;
}

.depth-effect {
  position: absolute;
  inset: 0;
  background: repeating-radial-gradient(
    circle at center,
    transparent,
    transparent 10px,
    rgba(0, 0, 0, 0.3) 10px,
    rgba(0, 0, 0, 0.3) 20px
  );
  transform: translateZ(-50px);
}

.mole-graphic,
.hole-graphic {
  position: relative;
  font-size: clamp(2.5rem, 10vw, 5rem);
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  text-shadow: 2px 2px 0 var(--accent-color), -2px -2px 0 var(--accent-color);
}

.hole.mole .mole-graphic {
  animation: popUp 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.hole.golden .mole-graphic {
  filter: brightness(1.5) sepia(1) hue-rotate(320deg);
  text-shadow: 0 0 10px gold, 0 0 20px gold, 0 0 30px gold;
  animation: goldenGlow 1s infinite alternate;
}

.hit-effect {
  position: absolute;
  inset: -50%;
  background: radial-gradient(
    circle at center,
    transparent 30%,
    rgba(255, 0, 0, 0.2) 70%
  );
  opacity: 0;
  transform: scale(0.5);
  transition: all 0.2s;
}

.hole:active .hit-effect {
  opacity: 1;
  transform: scale(1.5);
}

.hole:active {
  transform: scale(0.95) translateY(2px);
  border-color: var(--accent-color);
}

.hole:active .mole-graphic {
  transform: scale(0.8) rotate(-5deg);
  filter: brightness(0.8) contrast(1.2);
}

.hole:active .hole-inner {
  transform: translateZ(-20px);
}

.hole::before {
  content: "";
  position: absolute;
  inset: 0;
  background: radial-gradient(
    circle at center,
    transparent 30%,
    rgba(0, 0, 0, 0.8) 100%
  );
  pointer-events: none;
}

.hole::after {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(
    45deg,
    transparent 0%,
    rgba(255, 255, 255, 0.1) 50%,
    transparent 100%
  );
  animation: shine 4s linear infinite;
}

@keyframes shine {
  0% {
    transform: translateX(-100%) translateY(-100%) rotate(45deg);
  }
  100% {
    transform: translateX(100%) translateY(100%) rotate(45deg);
  }
}

@keyframes popUp {
  0% {
    transform: translateY(100%) scale(0.5);
    filter: brightness(0.5);
  }
  50% {
    transform: translateY(-20%) scale(1.1);
    filter: brightness(1.2);
  }
  100% {
    transform: translateY(0) scale(1);
    filter: brightness(1);
  }
}

@keyframes goldenGlow {
  from {
    filter: brightness(1.5) sepia(1) hue-rotate(320deg);
    text-shadow: 0 0 10px gold, 0 0 20px gold, 0 0 30px gold;
  }
  to {
    filter: brightness(2) sepia(1) hue-rotate(300deg);
    text-shadow: 0 0 15px gold, 0 0 25px gold, 0 0 35px gold;
  }
}

@media (max-width: 768px) {
  .mole-graphic,
  .hole-graphic {
    font-size: clamp(2rem, 8vw, 3rem);
  }

  .hole {
    border-width: 2px;
  }
}
</style>
