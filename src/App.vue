<script setup>
import { ref, onMounted, computed } from "vue";
import WorshipSection from "./components/WorshipSection.vue";
import HistorySection from "./components/HistorySection.vue";

const worshipRecords = ref([]);
const showContent = ref(false);
const activeQuote = ref(0);

const STORAGE_KEY = "cyber-wuhou-shrine-records";

// 诸葛亮名句
const quotes = [
  { text: "非淡泊无以明志，非宁静无以致远", source: "诫子书" },
  { text: "鞠躬尽瘁，死而后已", source: "后出师表" },
  { text: "受任于败军之际，奉命于危难之间", source: "出师表" },
  { text: "静以修身，俭以养德", source: "诫子书" },
  { text: "苟全性命于乱世，不求闻达于诸侯", source: "出师表" },
];

// 当前名句
const currentQuote = computed(() => quotes[activeQuote.value]);

// 名句轮播
let quoteTimer = null;
function startQuoteRotation() {
  quoteTimer = setInterval(() => {
    activeQuote.value = (activeQuote.value + 1) % quotes.length;
  }, 8000);
}

// 加载历史记录
function loadRecords() {
  try {
    const saved = localStorage.getItem(STORAGE_KEY);
    if (saved) {
      worshipRecords.value = JSON.parse(saved);
    }
  } catch (e) {
    console.warn("加载祭拜记录失败", e);
  }
}

// 保存历史记录
function saveRecords() {
  try {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(worshipRecords.value));
  } catch (e) {
    console.warn("保存祭拜记录失败", e);
  }
}

// 处理祭拜事件
function handleWorship(record) {
  worshipRecords.value.unshift(record);
  saveRecords();
}

onMounted(() => {
  loadRecords();
  startQuoteRotation();
  // 入场动画延迟
  setTimeout(() => {
    showContent.value = true;
  }, 300);
});
</script>

<template>
  <div class="shrine-app" :class="{ visible: showContent }">
    <!-- 背景装饰 -->
    <div class="bg-layer">
      <div class="bg-grid"></div>
      <div class="bg-glow bg-glow-1"></div>
      <div class="bg-glow bg-glow-2"></div>
      <div class="floating-particles">
        <span
          v-for="i in 20"
          :key="i"
          class="float-particle"
          :style="{
            '--size': `${Math.random() * 3 + 1}px`,
            '--left': `${Math.random() * 100}%`,
            '--delay': `${Math.random() * 10}s`,
            '--duration': `${Math.random() * 15 + 10}s`,
            '--opacity': Math.random() * 0.3 + 0.1,
          }"
        ></span>
      </div>
    </div>

    <!-- 主内容 -->
    <div class="shrine-content">
      <!-- 祠门 -->
      <header class="shrine-header">
        <h1 class="shrine-title">
          <span class="title-char" style="--i: 0">赛</span>
          <span class="title-char" style="--i: 1">博</span>
          <span class="title-char" style="--i: 2">武</span>
          <span class="title-char" style="--i: 3">侯</span>
          <span class="title-char" style="--i: 4">祠</span>
        </h1>
        <p class="shrine-subtitle">千古风流 精神永存</p>
      </header>

      <!-- 武侯殿 -->
      <section class="shrine-hall">
        <div class="hall-frame">
          <!-- 牌位区域 -->
          <div class="spirit-tablet-section">
            <div class="tablet-frame">
              <div class="tablet-inner">
                <div class="tablet-top-ornament"></div>
                <div class="tablet-body">
                  <div class="tablet-header">漢 丞 相 武 鄉 侯</div>
                  <div class="tablet-center-char">忠武</div>
                  <div class="tablet-sub">諸葛亮 之位</div>
                </div>
                <div class="tablet-bottom-ornament"></div>
                <div class="tablet-light"></div>
              </div>
            </div>
            <!-- 名号 -->
            <div class="tablet-title">
              <span class="title-name">诸葛亮</span>
              <span class="title-dates">公元181 — 234</span>
            </div>
          </div>

          <!-- 名句 -->
          <div class="quote-section">
            <Transition name="quote" mode="out-in">
              <div class="quote-card" :key="activeQuote">
                <p class="quote-text">{{ currentQuote.text }}</p>
                <span class="quote-source"
                  >&#x2014;&#x2014; {{ currentQuote.source }}</span
                >
              </div>
            </Transition>
          </div>
        </div>
      </section>

      <!-- 分隔线 -->
      <div class="section-divider">
        <span class="divider-dot"></span>
        <span class="divider-line"></span>
        <span class="divider-ornament">❖</span>
        <span class="divider-line"></span>
        <span class="divider-dot"></span>
      </div>

      <!-- 祭拜区 -->
      <WorshipSection @worship="handleWorship" />

      <!-- 分隔线 -->
      <div class="section-divider">
        <span class="divider-dot"></span>
        <span class="divider-line"></span>
        <span class="divider-ornament">❖</span>
        <span class="divider-line"></span>
        <span class="divider-dot"></span>
      </div>

      <!-- 祭拜历史 -->
      <HistorySection :records="worshipRecords" />

      <!-- 页脚 -->
      <footer class="shrine-footer">
        <div class="footer-line"></div>
        <p class="footer-text">赛博武侯祠 — 千载忠魂，数字永存</p>
        <p class="footer-sub">
          Cyber Wuhou Shrine &copy; {{ new Date().getFullYear() }}
        </p>
      </footer>
    </div>
  </div>
</template>

<style>
/* 全局 CSS 变量与基础样式 */
:root {
  --color-bg: #0a0a0f;
  --color-surface: #111118;
  --color-gold: #d4af37;
  --color-gold-dim: rgba(212, 175, 55, 0.6);
  --color-text: #c8b88a;
  --color-text-muted: #8a7d5f;
  --color-border: rgba(212, 175, 55, 0.12);
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
}

body {
  background: var(--color-bg);
  color: var(--color-text);
  font-family: "Noto Serif SC", "Songti SC", serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  overflow-x: hidden;
}

::-webkit-scrollbar {
  width: 4px;
}
::-webkit-scrollbar-track {
  background: var(--color-bg);
}
::-webkit-scrollbar-thumb {
  background: rgba(212, 175, 55, 0.2);
  border-radius: 2px;
}

::selection {
  background: rgba(212, 175, 55, 0.3);
  color: var(--color-gold);
}
</style>

<style scoped>
/* 应用容器 */
.shrine-app {
  min-height: 100vh;
  position: relative;
  opacity: 0;
  transition: opacity 1s ease;
}

.shrine-app.visible {
  opacity: 1;
}

/* 背景层 */
.bg-layer {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 0;
}

.bg-grid {
  position: absolute;
  inset: 0;
  background-image: linear-gradient(
      rgba(212, 175, 55, 0.03) 1px,
      transparent 1px
    ),
    linear-gradient(90deg, rgba(212, 175, 55, 0.03) 1px, transparent 1px);
  background-size: 60px 60px;
  mask-image: radial-gradient(ellipse at center, black 30%, transparent 70%);
  -webkit-mask-image: radial-gradient(
    ellipse at center,
    black 30%,
    transparent 70%
  );
}

.bg-glow {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
}

.bg-glow-1 {
  width: 400px;
  height: 400px;
  top: -100px;
  left: 50%;
  transform: translateX(-50%);
  background: radial-gradient(
    circle,
    rgba(212, 175, 55, 0.06) 0%,
    transparent 70%
  );
}

.bg-glow-2 {
  width: 300px;
  height: 300px;
  bottom: 10%;
  right: -50px;
  background: radial-gradient(
    circle,
    rgba(139, 105, 20, 0.04) 0%,
    transparent 70%
  );
}

/* 浮动粒子 */
.floating-particles {
  position: absolute;
  inset: 0;
  overflow: hidden;
}

.float-particle {
  position: absolute;
  bottom: -10px;
  left: var(--left);
  width: var(--size);
  height: var(--size);
  background: var(--color-gold);
  border-radius: 50%;
  opacity: var(--opacity);
  animation: floatUp var(--duration) var(--delay) linear infinite;
}

@keyframes floatUp {
  0% {
    transform: translateY(0) translateX(0);
    opacity: 0;
  }
  10% {
    opacity: var(--opacity);
  }
  90% {
    opacity: var(--opacity);
  }
  100% {
    transform: translateY(-100vh) translateX(20px);
    opacity: 0;
  }
}

/* 主内容 */
.shrine-content {
  position: relative;
  z-index: 1;
  max-width: 680px;
  margin: 0 auto;
  padding: 2rem 1.5rem;
}

/* 祠门 Header */
.shrine-header {
  text-align: center;
  padding: 3rem 0 2rem;
}

.header-ornament {
  width: 200px;
  height: 20px;
  margin: 0 auto;
  position: relative;
}

.top-ornament::before {
  content: "┋";
  position: absolute;
  left: 50%;
  top: 0;
  transform: translateX(-50%);
  color: var(--color-gold-dim);
  font-size: 0.6rem;
  letter-spacing: 15px;
}

.bottom-ornament::before {
  content: "━ ━ ━ ☙ ❧ ━ ━ ━";
  position: absolute;
  left: 50%;
  top: 0;
  transform: translateX(-50%);
  color: rgba(212, 175, 55, 0.3);
  font-size: 0.6rem;
  letter-spacing: 3px;
  white-space: nowrap;
}

.shrine-title {
  font-family: "Ma Shan Zheng", cursive;
  font-size: 3.2rem;
  color: var(--color-gold);
  letter-spacing: 0.4em;
  margin: 1rem 0;
  text-shadow: 0 0 40px rgba(212, 175, 55, 0.3);
  display: flex;
  justify-content: center;
}

.title-char {
  display: inline-block;
  animation: titleCharIn 0.8s calc(var(--i) * 0.12s) both ease-out;
}

@keyframes titleCharIn {
  0% {
    opacity: 0;
    transform: translateY(-20px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

.shrine-subtitle {
  font-family: "Noto Serif SC", serif;
  font-size: 0.9rem;
  color: var(--color-text-muted);
  letter-spacing: 0.5em;
  margin-top: 0.5rem;
}

/* 武侯殿 */
.shrine-hall {
  padding: 1rem 0 2rem;
}

.hall-frame {
  background: rgba(255, 255, 255, 0.01);
  border: 1px solid var(--color-border);
  border-radius: 12px;
  padding: 2.5rem 1.5rem;
  position: relative;
  overflow: hidden;
}

.hall-frame::before {
  content: "";
  position: absolute;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 60%;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    var(--color-gold),
    transparent
  );
}

.hall-frame::after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 60%;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(212, 175, 55, 0.3),
    transparent
  );
}

/* 牌位 */
.spirit-tablet-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 2rem;
}

.tablet-frame {
  position: relative;
  padding: 3px;
  background: linear-gradient(
    180deg,
    rgba(212, 175, 55, 0.35),
    rgba(212, 175, 55, 0.08) 40%,
    rgba(212, 175, 55, 0.15)
  );
  border-radius: 4px 4px 2px 2px;
}

.tablet-inner {
  width: 220px;
  min-height: 280px;
  background: linear-gradient(180deg, #14120e 0%, #0f0e0a 30%, #0c0b08 100%);
  border-radius: 3px 3px 1px 1px;
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
  overflow: hidden;
  padding: 2rem 1.5rem;
}

.tablet-top-ornament {
  position: absolute;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 14px solid transparent;
  border-right: 14px solid transparent;
  border-top: 10px solid rgba(212, 175, 55, 0.3);
}

.tablet-bottom-ornament {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 6px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(212, 175, 55, 0.2),
    transparent
  );
}

.tablet-body {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  flex: 1;
  z-index: 1;
}

.tablet-header {
  font-family: "Noto Serif SC", serif;
  font-size: 0.8rem;
  color: rgba(212, 175, 55, 0.5);
  letter-spacing: 0.4em;
  border-bottom: 1px solid rgba(212, 175, 55, 0.15);
  padding-bottom: 0.6rem;
}

.tablet-center-char {
  font-family: "Ma Shan Zheng", cursive;
  font-size: 4rem;
  color: var(--color-gold);
  letter-spacing: 0.3em;
  text-shadow: 0 0 30px rgba(212, 175, 55, 0.3),
    0 0 60px rgba(212, 175, 55, 0.1);
  line-height: 1.2;
  animation: tabletGlow 4s ease-in-out infinite;
}

@keyframes tabletGlow {
  0%,
  100% {
    text-shadow: 0 0 30px rgba(212, 175, 55, 0.3),
      0 0 60px rgba(212, 175, 55, 0.1);
  }
  50% {
    text-shadow: 0 0 40px rgba(212, 175, 55, 0.5),
      0 0 80px rgba(212, 175, 55, 0.2);
  }
}

.tablet-sub {
  font-family: "Noto Serif SC", serif;
  font-size: 0.95rem;
  color: rgba(212, 175, 55, 0.65);
  letter-spacing: 0.5em;
}

.tablet-light {
  position: absolute;
  top: 30%;
  left: 50%;
  transform: translateX(-50%);
  width: 120px;
  height: 120px;
  background: radial-gradient(
    circle,
    rgba(212, 175, 55, 0.08) 0%,
    transparent 70%
  );
  border-radius: 50%;
  animation: tabletLightPulse 4s ease-in-out infinite;
  pointer-events: none;
}

@keyframes tabletLightPulse {
  0%,
  100% {
    opacity: 0.4;
    transform: translateX(-50%) scale(1);
  }
  50% {
    opacity: 1;
    transform: translateX(-50%) scale(1.3);
  }
}

/* 牌位名号 */
.tablet-title {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.3rem;
  margin-top: 1.2rem;
}

.title-name {
  font-family: "Ma Shan Zheng", cursive;
  font-size: 2.2rem;
  color: var(--color-gold);
  letter-spacing: 0.5em;
  text-shadow: 0 0 20px rgba(212, 175, 55, 0.2);
}

.title-dates {
  font-size: 0.75rem;
  color: rgba(180, 160, 120, 0.4);
  letter-spacing: 0.2em;
}

/* 名句区 */
.quote-section {
  max-width: 460px;
  margin: 0 auto;
  min-height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.quote-card {
  text-align: center;
  padding: 1.5rem;
  position: relative;
}

.quote-mark {
  font-family: "Ma Shan Zheng", cursive;
  font-size: 2rem;
  color: rgba(212, 175, 55, 0.2);
  line-height: 1;
}

.quote-mark.end {
  text-align: right;
}

.quote-text {
  font-family: "Ma Shan Zheng", cursive;
  font-size: 1.5rem;
  color: var(--color-text);
  letter-spacing: 0.15em;
  padding: 0.5rem 0;
  line-height: 1.8;
}

.quote-source {
  font-family: "Noto Serif SC", serif;
  font-size: 0.8rem;
  color: var(--color-text-muted);
  letter-spacing: 0.1em;
}

/* 名句过渡 */
.quote-enter-active {
  transition: all 0.6s ease;
}
.quote-leave-active {
  transition: all 0.4s ease;
}
.quote-enter-from {
  opacity: 0;
  transform: translateY(10px);
}
.quote-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

/* 分隔线 */
.section-divider {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  padding: 1.5rem 0;
}

.divider-dot {
  width: 3px;
  height: 3px;
  border-radius: 50%;
  background: rgba(212, 175, 55, 0.3);
}

.divider-line {
  flex: 1;
  max-width: 100px;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(212, 175, 55, 0.2),
    transparent
  );
}

.divider-ornament {
  color: rgba(212, 175, 55, 0.25);
  font-size: 0.7rem;
}

/* 页脚 */
.shrine-footer {
  text-align: center;
  padding: 3rem 0 2rem;
}

.footer-line {
  width: 100px;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(212, 175, 55, 0.15),
    transparent
  );
  margin: 0 auto 1.5rem;
}

.footer-text {
  font-family: "Ma Shan Zheng", cursive;
  font-size: 0.9rem;
  color: var(--color-text-muted);
  letter-spacing: 0.2em;
}

.footer-sub {
  font-size: 0.7rem;
  color: rgba(180, 160, 120, 0.3);
  margin-top: 0.5rem;
  letter-spacing: 0.1em;
}

/* 响应式 */
@media (max-width: 640px) {
  .shrine-content {
    padding: 1rem;
  }

  .shrine-title {
    font-size: 2.4rem;
    letter-spacing: 0.2em;
  }

  .hall-frame {
    padding: 1.5rem 1rem;
  }

  .tablet-inner {
    width: 180px;
    min-height: 230px;
  }

  .tablet-center-char {
    font-size: 3rem;
  }

  .title-name {
    font-size: 1.8rem;
  }

  .quote-text {
    font-size: 1.2rem;
  }
}
</style>
