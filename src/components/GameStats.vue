<script setup lang="ts">
defineProps<{
  highScore: number;
  streak: number;
  maxStreak: number;
}>();
</script>

<template>
  <div class="stats">
    <div class="stat-container high-score-container">
      <div class="glitch-overlay"></div>
      <div class="high-score">HIGH SCORE: {{ highScore }}</div>
    </div>
    <div class="stat-container streak-container" v-if="streak > 0">
      <div class="glitch-overlay"></div>
      <div class="streak">
        <span class="streak-label">STREAK</span>
        <span class="streak-value">{{ streak }}</span>
        <span class="max-streak">BEST: {{ maxStreak }}</span>
      </div>
      <div class="streak-particles"></div>
    </div>
  </div>
</template>

<style scoped>
.stats {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  width: 100%;
  font-size: clamp(0.9rem, 2.5vw, 1.3rem);
  text-transform: uppercase;
}

.stat-container {
  position: relative;
  border: 2px dashed var(--text-color);
  padding: 0.5em;
  background: var(--main-bg);
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

.high-score,
.streak {
  position: relative;
  color: var(--accent-color);
  text-shadow: 2px 2px 0 var(--main-bg), -1px -1px 0 var(--text-color);
  z-index: 2;
}

.streak {
  display: flex;
  flex-direction: column;
  align-items: center;
  animation: pulseStreak 2s infinite;
}

.streak-label {
  font-size: 0.8em;
  letter-spacing: 0.2em;
  opacity: 0.8;
}

.streak-value {
  font-size: 1.3em;
  font-weight: bold;
}

.max-streak {
  font-size: 0.7em;
  opacity: 0.8;
  color: var(--power-up-color);
  text-shadow: 1px 1px 0 var(--accent-color);
  margin-top: 0.2em;
}

.glitch-overlay {
  position: absolute;
  inset: 0;
  background: repeating-linear-gradient(
    -45deg,
    transparent,
    transparent 10px,
    rgba(255, 0, 0, 0.05) 10px,
    rgba(255, 0, 0, 0.05) 20px
  );
  z-index: 1;
  mix-blend-mode: overlay;
  animation: glitchMove 8s linear infinite;
}

.streak-particles {
  position: absolute;
  inset: 0;
  background: radial-gradient(
      circle at 20% 20%,
      rgba(255, 0, 0, 0.1) 0%,
      transparent 20%
    ),
    radial-gradient(circle at 80% 80%, rgba(255, 0, 0, 0.1) 0%, transparent 20%),
    radial-gradient(circle at 50% 50%, rgba(255, 0, 0, 0.1) 0%, transparent 30%);
  filter: blur(1px);
  animation: particlesFloat 4s ease-in-out infinite;
  z-index: 0;
}

@keyframes pulseStreak {
  0%,
  100% {
    transform: scale(1);
    filter: brightness(1);
  }
  50% {
    transform: scale(1.02);
    filter: brightness(1.2);
  }
}

@keyframes glitchMove {
  0% {
    transform: translateX(-50%) translateY(-50%) rotate(0deg);
    opacity: 0.5;
  }
  50% {
    transform: translateX(50%) translateY(50%) rotate(180deg);
    opacity: 0.8;
  }
  100% {
    transform: translateX(-50%) translateY(-50%) rotate(360deg);
    opacity: 0.5;
  }
}

@keyframes particlesFloat {
  0%,
  100% {
    transform: translateY(0) scale(1);
    opacity: 0.5;
  }
  50% {
    transform: translateY(-10px) scale(1.1);
    opacity: 0.8;
  }
}

.high-score-container:hover,
.streak-container:hover {
  border-style: solid;
  border-color: var(--accent-color);
  transform: scale(1.02);
  transition: all 0.3s ease;
}

.stat-container::before {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(
    45deg,
    transparent,
    rgba(255, 0, 0, 0.1),
    transparent
  );
  z-index: 1;
  animation: shimmer 2s linear infinite;
}

@keyframes shimmer {
  0% {
    transform: translateX(-100%) rotate(45deg);
  }
  100% {
    transform: translateX(100%) rotate(45deg);
  }
}

@media (max-width: 768px) {
  .stats {
    gap: 0.5rem;
  }

  .stat-container {
    border-width: 1px;
    padding: 0.3em;
  }

  .streak-value {
    font-size: 1.2em;
  }
}
</style>
