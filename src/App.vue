<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import ScoreBoard from "./components/ScoreBoard.vue";
import GameStats from "./components/GameStats.vue";
import PowerUpIndicator from "./components/PowerUpIndicator.vue";
import GameBoard from "./components/GameBoard.vue";
import StartButton from "./components/StartButton.vue";

const score = ref(0);
const timeLeft = ref(30);
const gameActive = ref(false);
const holes = ref(Array(9).fill(false));
const timer = ref<number | null>(null);
const moleTimer = ref<number | null>(null);
const highScore = ref(parseInt(localStorage.getItem("highScore") || "0"));
const combo = ref(0);
const lastWhackTime = ref(0);
const powerUpActive = ref(false);
const powerUpTimer = ref<number | null>(null);
const animals = ["🦔", "🦊", "🦡", "🦫", "🦘"];
const currentAnimal = ref(animals[0]);
const streak = ref(0);
const maxStreak = ref(0);
const missedHits = ref(0);
const isGoldenMole = ref(false);
const difficulty = ref(1);

const gameStartSound = new Audio("/sounds/game-start.mp3");
const gameEndSound = new Audio("/sounds/game-end.mp3");
const hitGroundSound = new Audio("/sounds/hit-ground.mp3");
const hitWhackSound = new Audio("/sounds/hit-whack.mp3");

const startGame = () => {
  gameStartSound.play();
  score.value = 0;
  timeLeft.value = 30;
  gameActive.value = true;
  holes.value = Array(9).fill(false);
  combo.value = 0;
  streak.value = 0;
  maxStreak.value = 0;
  missedHits.value = 0;
  powerUpActive.value = false;
  difficulty.value = 1;
  isGoldenMole.value = false;

  timer.value = setInterval(() => {
    timeLeft.value--;
    if (timeLeft.value <= 0) {
      endGame();
    }
    // INCREASE DIFFICULTY EVERY 10 SECS
    if (timeLeft.value % 10 === 0) {
      difficulty.value = Math.min(difficulty.value + 0.2, 2.5);
    }
  }, 1000) as unknown as number;

  moveMole();
};

const endGame = () => {
  gameEndSound.play();
  gameActive.value = false;
  if (timer.value) clearInterval(timer.value);
  if (moleTimer.value) clearInterval(moleTimer.value);
  if (powerUpTimer.value) clearInterval(powerUpTimer.value);
  holes.value = Array(9).fill(false);
  if (score.value > highScore.value) {
    highScore.value = score.value;
    localStorage.setItem("highScore", highScore.value.toString());
  }
  localStorage.setItem("maxStreak", maxStreak.value.toString());
};

const activatePowerUp = () => {
  powerUpActive.value = true;
  if (powerUpTimer.value) clearInterval(powerUpTimer.value);
  powerUpTimer.value = setInterval(() => {
    powerUpActive.value = false;
  }, 5000) as unknown as number;
};

const changeAnimal = () => {
  const randomIndex = Math.floor(Math.random() * animals.length);
  currentAnimal.value = animals[randomIndex];
};

const moveMole = () => {
  moleTimer.value = setInterval(() => {
    holes.value = holes.value.map(() => false);
    const randomHole = Math.floor(Math.random() * 9);
    holes.value[randomHole] = true;

    isGoldenMole.value = Math.random() < 0.1; // 10%

    if (Math.random() < 0.2) {
      changeAnimal();
      if (Math.random() < 0.3) {
        activatePowerUp();
      }
    }
  }, Math.max(400, 1000 - score.value * 20) / difficulty.value) as unknown as number;
};

const whackMole = (index: number) => {
  if (holes.value[index] && gameActive.value) {
    hitWhackSound.play();
    const currentTime = Date.now();
    if (currentTime - lastWhackTime.value < 1000) {
      combo.value++;
    } else {
      combo.value = 1;
    }
    lastWhackTime.value = currentTime;

    let points = combo.value;
    if (powerUpActive.value) points *= 2;
    if (isGoldenMole.value) points *= 3;
    score.value += points;

    streak.value++;
    maxStreak.value = Math.max(maxStreak.value, streak.value);

    if (streak.value % 5 === 0) {
      timeLeft.value = Math.min(timeLeft.value + 2, 30);
    }

    holes.value[index] = false;
    isGoldenMole.value = false;
  } else if (gameActive.value) {
    hitGroundSound.play();
    // failure penalties
    streak.value = 0;
    combo.value = 1;
    if (score.value > 0) {
      score.value = Math.max(0, score.value - 5);
    }
  }
};

onUnmounted(() => {
  if (timer.value) clearInterval(timer.value);
  if (moleTimer.value) clearInterval(moleTimer.value);
  if (powerUpTimer.value) clearInterval(powerUpTimer.value);
});
</script>

<template>
  <div class="game-container" :class="{ 'power-up': powerUpActive }">
    <div class="scan-effect"></div>
    <div class="noise-overlay"></div>
    <div class="crt-effect"></div>

    <h1>
      <span class="glitch-text" data-text="WHACK-A-MOLE">WHACK-A-MOLE</span>
    </h1>

    <ScoreBoard :score="score" :combo="combo" :timeLeft="timeLeft" />
    <GameStats :highScore="highScore" :streak="streak" :maxStreak="maxStreak" />
    <PowerUpIndicator :isActive="powerUpActive" />
    <GameBoard
      :holes="holes"
      :gameActive="gameActive"
      :currentAnimal="currentAnimal"
      :isGoldenMole="isGoldenMole"
      @whack="whackMole"
    />
    <StartButton :gameActive="gameActive" @start="startGame" />

    <a
      href="https://eduardoprofe666.github.io"
      target="_blank"
      rel="noopener noreferrer"
      class="portfolio-link"
    >
      <div class="portfolio-button">
        <div class="glitch-text" data-text="🎩 EduardoProfe666 🎩">
          🎩 EduardoProfe666 🎩
        </div>
        <div class="button-background"></div>
      </div>
    </a>
  </div>
</template>

<style>
:root {
  --main-bg: #000000;
  --text-color: #ffffff;
  --accent-color: #ff0000;
  --hole-color: #333333;
  --mole-color: #ff6b6b;
  --power-up-color: #ffff00;
  --glitch-color: #0ff;
  --scan-line-height: 4px;
}

body {
  margin: 0;
  background-color: var(--main-bg);
  color: var(--text-color);
  font-family: "Courier New", monospace;
  cursor: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='40' height='48' viewport='0 0 100 100' style='fill:black;font-size:24px;'><text y='50%'>🔨</text></svg>")
      16 0,
    auto;
  overflow-x: hidden;
}

.game-container {
  width: min(100%, 700px);
  height: max-content;
  margin: auto;
  padding: min(1.5rem, 3vw);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: min(1rem, 2vw);
  position: relative;
  isolation: isolate;
  border: 4px solid var(--text-color);
  background-color: var(--main-bg);
  box-shadow: 8px 8px 0 var(--accent-color), -1px -1px 0 var(--glitch-color),
    1px 1px 0 var(--accent-color);
}

h1 {
  font-size: clamp(1.8rem, 5vw, 2.8rem);
  margin-bottom: min(0.8rem, 1.5vw);
  letter-spacing: 2px;
}

.scan-effect {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  height: var(--scan-line-height);
  background: linear-gradient(
    to bottom,
    transparent,
    rgba(255, 0, 0, 0.1),
    transparent
  );
  animation: scan 8s linear infinite;
  pointer-events: none;
  z-index: 10;
}

.noise-overlay {
  position: fixed;
  inset: 0;
  background-image: url('data:image/svg+xml,<svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg"><filter id="noise"><feTurbulence type="fractalNoise" baseFrequency="0.8" numOctaves="4" stitchTiles="stitch"/></filter><rect width="100%" height="100%" filter="url(%23noise)" opacity="0.02"/></svg>');
  pointer-events: none;
  z-index: 9;
  mix-blend-mode: overlay;
}

.crt-effect {
  position: fixed;
  inset: 0;
  background: linear-gradient(
    to bottom,
    rgba(255, 0, 0, 0.1),
    rgba(0, 255, 255, 0.1)
  );
  mix-blend-mode: overlay;
  pointer-events: none;
  z-index: 8;
  animation: flicker 0.15s infinite;
}

.glitch-text {
  position: relative;
  display: inline-block;
  animation: textShadow 2s infinite;
}

.glitch-text::before,
.glitch-text::after {
  content: attr(data-text);
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
}

.glitch-text::before {
  left: 2px;
  text-shadow: -2px 0 var(--accent-color);
  animation: glitch-1 3s infinite linear alternate-reverse;
  clip-path: polygon(0 0, 100% 0, 100% 45%, 0 45%);
}

.glitch-text::after {
  left: -2px;
  text-shadow: 2px 0 var(--glitch-color);
  animation: glitch-2 2.7s infinite linear alternate-reverse;
  clip-path: polygon(0 55%, 100% 55%, 100% 100%, 0 100%);
}

.game-container.power-up {
  animation: powerUpPulse 1s infinite;
  box-shadow: 0 0 20px rgba(255, 255, 0, 0.2), 0 0 40px rgba(255, 255, 0, 0.1),
    0 0 60px rgba(255, 255, 0, 0.1);
}

.portfolio-link {
  text-decoration: none;
  color: inherit;
  margin-top: 1rem;
}

.portfolio-button {
  font-size: clamp(1rem, 3vw, 1.4rem);
  padding: 0.6em 1.2em;
  background-color: var(--main-bg);
  color: var(--text-color);
  border: 3px solid var(--text-color);
  position: relative;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
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

.portfolio-button::before {
  content: "";
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

.portfolio-button:hover {
  transform: translate(-2px, -2px) scale(1.02);
  box-shadow: 2px 2px 0 var(--text-color), 4px 4px 0 var(--accent-color),
    6px 6px 10px rgba(255, 0, 0, 0.3);
  border-color: var(--accent-color);
}

.portfolio-button:hover .glitch-text {
  animation: glitch-1 0.5s infinite;
}

.portfolio-button:active {
  transform: translate(1px, 1px) scale(0.98);
  box-shadow: none;
}

@keyframes scan {
  0% {
    top: -100%;
  }
  100% {
    top: 100%;
  }
}

@keyframes flicker {
  0% {
    opacity: 0.27861;
  }
  5% {
    opacity: 0.34769;
  }
  10% {
    opacity: 0.23604;
  }
  15% {
    opacity: 0.90626;
  }
  20% {
    opacity: 0.18128;
  }
  25% {
    opacity: 0.83891;
  }
  30% {
    opacity: 0.65583;
  }
  35% {
    opacity: 0.67807;
  }
  40% {
    opacity: 0.26559;
  }
  45% {
    opacity: 0.84693;
  }
  50% {
    opacity: 0.96019;
  }
  55% {
    opacity: 0.08594;
  }
  60% {
    opacity: 0.20313;
  }
  65% {
    opacity: 0.71988;
  }
  70% {
    opacity: 0.53455;
  }
  75% {
    opacity: 0.37288;
  }
  80% {
    opacity: 0.71428;
  }
  85% {
    opacity: 0.70419;
  }
  90% {
    opacity: 0.7003;
  }
  95% {
    opacity: 0.36108;
  }
  100% {
    opacity: 0.24387;
  }
}

@keyframes textShadow {
  0% {
    text-shadow: 0.4389px 0 1px rgba(0, 30, 255, 0.5),
      -0.4389px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  5% {
    text-shadow: 2.7928px 0 1px rgba(0, 30, 255, 0.5),
      -2.7928px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  10% {
    text-shadow: 0.02956px 0 1px rgba(0, 30, 255, 0.5),
      -0.02956px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  15% {
    text-shadow: 0.40219px 0 1px rgba(0, 30, 255, 0.5),
      -0.40219px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  20% {
    text-shadow: 3.4794px 0 1px rgba(0, 30, 255, 0.5),
      -3.4794px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  25% {
    text-shadow: 1.6125px 0 1px rgba(0, 30, 255, 0.5),
      -1.6125px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  30% {
    text-shadow: 0.7015px 0 1px rgba(0, 30, 255, 0.5),
      -0.7015px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  35% {
    text-shadow: 3.896px 0 1px rgba(0, 30, 255, 0.5),
      -3.896px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  40% {
    text-shadow: 3.87px 0 1px rgba(0, 30, 255, 0.5),
      -3.87px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  45% {
    text-shadow: 2.231px 0 1px rgba(0, 30, 255, 0.5),
      -2.231px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  50% {
    text-shadow: 0.08084px 0 1px rgba(0, 30, 255, 0.5),
      -0.08084px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  55% {
    text-shadow: 2.3758px 0 1px rgba(0, 30, 255, 0.5),
      -2.3758px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  60% {
    text-shadow: 2.202px 0 1px rgba(0, 30, 255, 0.5),
      -2.202px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  65% {
    text-shadow: 2.546px 0 1px rgba(0, 30, 255, 0.5),
      -2.546px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  70% {
    text-shadow: 0.98693px 0 1px rgba(0, 30, 255, 0.5),
      -0.98693px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  75% {
    text-shadow: 1.73897px 0 1px rgba(0, 30, 255, 0.5),
      -1.73897px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  80% {
    text-shadow: 0.91795px 0 1px rgba(0, 30, 255, 0.5),
      -0.91795px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  85% {
    text-shadow: 0.89586px 0 1px rgba(0, 30, 255, 0.5),
      -0.89586px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  90% {
    text-shadow: 1.27673px 0 1px rgba(0, 30, 255, 0.5),
      -1.27673px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  95% {
    text-shadow: 0.08084px 0 1px rgba(0, 30, 255, 0.5),
      -0.08084px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
  100% {
    text-shadow: 2.45081px 0 1px rgba(0, 30, 255, 0.5),
      -2.45081px 0 1px rgba(255, 0, 80, 0.3), 0 0 3px;
  }
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

@keyframes powerUpPulse {
  0%,
  100% {
    transform: scale(1);
    filter: brightness(1);
  }
  50% {
    transform: scale(1.01);
    filter: brightness(1.1);
  }
}

@media (max-width: 768px) {
  .game-container {
    padding: min(1rem, 3vw);
    gap: min(1rem, 2vw);
    border-width: 3px;
  }

  h1 {
    font-size: clamp(1.5rem, 5vw, 2.5rem);
    margin-bottom: 0.5rem;
  }

  .portfolio-button {
    font-size: clamp(0.9rem, 2.5vw, 1.2rem);
    padding: 0.4em 0.8em;
    border-width: 2px;
  }
}
</style>
