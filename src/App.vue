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

const currentQuote = computed(() => quotes[activeQuote.value]);

let quoteTimer = null;
function startQuoteRotation() {
  quoteTimer = setInterval(() => {
    activeQuote.value = (activeQuote.value + 1) % quotes.length;
  }, 8000);
}

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

function saveRecords() {
  try {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(worshipRecords.value));
  } catch (e) {
    console.warn("保存祭拜记录失败", e);
  }
}

function handleWorship(record) {
  worshipRecords.value.unshift(record);
  saveRecords();
}

onMounted(() => {
  loadRecords();
  startQuoteRotation();
  setTimeout(() => {
    showContent.value = true;
  }, 300);
});
</script>

<template>
  <div class="shrine-app" :class="{ visible: showContent }">
    <!-- 背景装饰：宣纸 + 远山 + 浮萤 -->
    <div class="bg-layer">
      <div class="bg-paper"></div>
      <div class="bg-mist bg-mist-1"></div>
      <div class="bg-mist bg-mist-2"></div>
      <div class="bg-mist bg-mist-3"></div>
      <div class="floating-particles">
        <span
          v-for="i in 18"
          :key="i"
          class="float-particle"
          :style="{
            '--size': `${Math.random() * 2 + 1}px`,
            '--left': `${Math.random() * 100}%`,
            '--delay': `${Math.random() * 12}s`,
            '--duration': `${Math.random() * 18 + 14}s`,
            '--opacity': Math.random() * 0.18 + 0.06,
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
      </header>

      <!-- 武侯殿 -->
      <section class="shrine-hall">
        <div class="hall-frame">
          <span class="corner corner-tl"></span>
          <span class="corner corner-tr"></span>
          <span class="corner corner-bl"></span>
          <span class="corner corner-br"></span>

          <!-- 牌位区域 -->
          <div class="spirit-tablet-section">
            <div class="tablet-frame">
              <div class="tablet-inner">
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
              <span class="title-dates">公元 181 — 234</span>
            </div>
          </div>

          <!-- 名句 -->
          <div class="quote-section">
            <Transition name="quote" mode="out-in">
              <div class="quote-card" :key="activeQuote">
                <span class="quote-quote-mark">「</span>
                <p class="quote-text">{{ currentQuote.text }}</p>
                <span class="quote-quote-mark end">」</span>
                <span class="quote-source"
                  >&#x2014;&#x2014;&nbsp;{{ currentQuote.source }}</span
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
        <p class="footer-text">赛博武侯祠 — 千载忠魂&nbsp;&nbsp;数字永存</p>
        <p class="footer-sub">
          Cyber Wuhou Shrine &copy; {{ new Date().getFullYear() }}
        </p>
      </footer>
    </div>
  </div>
</template>

<style>
/* 全局 CSS 变量：清雅水墨配色 */
:root {
  --color-bg: #f4efe6;            /* 宣纸底 */
  --color-bg-soft: #ebe3d4;       /* 浅米卡其 */
  --color-surface: #faf6ee;       /* 浮纸面 */
  --color-ink: #2a2620;           /* 墨色主文字 */
  --color-ink-soft: #5a4f3f;      /* 淡墨 */
  --color-ink-mute: #8a7d68;      /* 远墨 */
  --color-gold: #a37a3a;          /* 古铜金，含蓄 */
  --color-gold-soft: #c8a566;     /* 浅金 */
  --color-gold-faint: rgba(163, 122, 58, 0.18);
  --color-cinnabar: #9a4b3b;      /* 朱砂胭脂 */
  --color-celadon: #6b8a7a;       /* 青瓷绿 */
  --color-border: rgba(90, 79, 63, 0.14);
  --color-border-soft: rgba(90, 79, 63, 0.08);
  --shadow-soft: 0 1px 2px rgba(60, 50, 35, 0.04),
    0 8px 24px rgba(60, 50, 35, 0.05);
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
  color: var(--color-ink);
  font-family: "Noto Serif SC", "Songti SC", "STSong", serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  overflow-x: hidden;
  font-weight: 400;
}

::-webkit-scrollbar {
  width: 6px;
}
::-webkit-scrollbar-track {
  background: transparent;
}
::-webkit-scrollbar-thumb {
  background: rgba(163, 122, 58, 0.2);
  border-radius: 3px;
}
::-webkit-scrollbar-thumb:hover {
  background: rgba(163, 122, 58, 0.35);
}

::selection {
  background: rgba(163, 122, 58, 0.18);
  color: var(--color-cinnabar);
}
</style>

<style scoped>
/* 应用容器 */
.shrine-app {
  min-height: 100vh;
  position: relative;
  opacity: 0;
  transition: opacity 1.2s ease;
}

.shrine-app.visible {
  opacity: 1;
}

/* 背景层：宣纸纹 + 雾霭 */
.bg-layer {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 0;
}

.bg-paper {
  position: absolute;
  inset: 0;
  background-color: var(--color-bg);
  background-image:
    radial-gradient(
      ellipse at 20% 10%,
      rgba(255, 248, 232, 0.6) 0%,
      transparent 55%
    ),
    radial-gradient(
      ellipse at 80% 100%,
      rgba(212, 195, 160, 0.18) 0%,
      transparent 60%
    ),
    repeating-linear-gradient(
      45deg,
      rgba(120, 95, 60, 0.018) 0px,
      rgba(120, 95, 60, 0.018) 1px,
      transparent 1px,
      transparent 4px
    ),
    repeating-linear-gradient(
      -45deg,
      rgba(120, 95, 60, 0.012) 0px,
      rgba(120, 95, 60, 0.012) 1px,
      transparent 1px,
      transparent 5px
    );
}

.bg-mist {
  position: absolute;
  border-radius: 50%;
  filter: blur(90px);
  opacity: 0.55;
}

.bg-mist-1 {
  width: 480px;
  height: 480px;
  top: -120px;
  left: 50%;
  transform: translateX(-50%);
  background: radial-gradient(
    circle,
    rgba(200, 165, 102, 0.18) 0%,
    transparent 70%
  );
}

.bg-mist-2 {
  width: 360px;
  height: 360px;
  bottom: 8%;
  right: -80px;
  background: radial-gradient(
    circle,
    rgba(107, 138, 122, 0.14) 0%,
    transparent 70%
  );
}

.bg-mist-3 {
  width: 320px;
  height: 320px;
  top: 40%;
  left: -60px;
  background: radial-gradient(
    circle,
    rgba(154, 75, 59, 0.08) 0%,
    transparent 70%
  );
}

/* 浮萤 */
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
  background: var(--color-gold-soft);
  border-radius: 50%;
  opacity: var(--opacity);
  box-shadow: 0 0 6px rgba(200, 165, 102, 0.35);
  animation: floatUp var(--duration) var(--delay) linear infinite;
}

@keyframes floatUp {
  0% {
    transform: translateY(0) translateX(0);
    opacity: 0;
  }
  10% { opacity: var(--opacity); }
  90% { opacity: var(--opacity); }
  100% {
    transform: translateY(-100vh) translateX(30px);
    opacity: 0;
  }
}

/* 主内容 */
.shrine-content {
  position: relative;
  z-index: 1;
  max-width: 680px;
  margin: 0 auto;
  padding: 2.5rem 1.75rem;
}

/* 祠门 Header */
.shrine-header {
  text-align: center;
  padding: 2.5rem 0 2rem;
}

.header-seal {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.seal-line {
  width: 48px;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(163, 122, 58, 0.4),
    transparent
  );
}

.seal-mark {
  font-family: "Ma Shan Zheng", "STKaiti", cursive;
  color: var(--color-cinnabar);
  font-size: 0.95rem;
  letter-spacing: 0.2em;
  border: 1px solid rgba(154, 75, 59, 0.4);
  padding: 0.15rem 0.45rem;
  border-radius: 2px;
  background: rgba(154, 75, 59, 0.04);
  opacity: 0.85;
}

.shrine-title {
  font-family: "Ma Shan Zheng", "STKaiti", cursive;
  font-size: 3rem;
  color: var(--color-ink);
  letter-spacing: 0.45em;
  margin: 0.5rem 0;
  text-shadow: 0 1px 0 rgba(255, 255, 255, 0.4);
  display: flex;
  justify-content: center;
  font-weight: 400;
}

.title-char {
  display: inline-block;
  animation: titleCharIn 0.9s calc(var(--i) * 0.13s) both ease-out;
  position: relative;
}

@keyframes titleCharIn {
  0% {
    opacity: 0;
    transform: translateY(-16px);
    filter: blur(4px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
    filter: blur(0);
  }
}

.shrine-subtitle {
  font-family: "Noto Serif SC", serif;
  font-size: 0.85rem;
  color: var(--color-ink-mute);
  letter-spacing: 0.55em;
  margin-top: 0.75rem;
  font-weight: 300;
}

/* 武侯殿 */
.shrine-hall {
  padding: 1.25rem 0 2rem;
}

.hall-frame {
  background:
    linear-gradient(180deg, rgba(250, 246, 238, 0.85), rgba(244, 239, 230, 0.7));
  border: 1px solid var(--color-border);
  border-radius: 4px;
  padding: 3rem 1.75rem 2.5rem;
  position: relative;
  box-shadow: var(--shadow-soft);
  backdrop-filter: blur(2px);
}

.hall-frame::before {
  content: "";
  position: absolute;
  inset: 6px;
  border: 1px solid rgba(163, 122, 58, 0.12);
  border-radius: 2px;
  pointer-events: none;
}

/* 四角小装饰 */
.corner {
  position: absolute;
  width: 14px;
  height: 14px;
  border-color: rgba(163, 122, 58, 0.45);
  border-style: solid;
  border-width: 0;
  pointer-events: none;
}
.corner-tl { top: 10px; left: 10px; border-top-width: 1px; border-left-width: 1px; }
.corner-tr { top: 10px; right: 10px; border-top-width: 1px; border-right-width: 1px; }
.corner-bl { bottom: 10px; left: 10px; border-bottom-width: 1px; border-left-width: 1px; }
.corner-br { bottom: 10px; right: 10px; border-bottom-width: 1px; border-right-width: 1px; }

/* 牌位 */
.spirit-tablet-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 1.75rem;
}

.tablet-frame {
  position: relative;
  padding: 2px;
  background: linear-gradient(
    180deg,
    rgba(163, 122, 58, 0.55) 0%,
    rgba(200, 165, 102, 0.18) 50%,
    rgba(163, 122, 58, 0.35) 100%
  );
  border-radius: 6px 6px 3px 3px;
  box-shadow:
    0 2px 4px rgba(60, 50, 35, 0.06),
    0 12px 28px rgba(60, 50, 35, 0.08);
}

.tablet-inner {
  width: 220px;
  min-height: 290px;
  background:
    radial-gradient(ellipse at top, #fbf6ea 0%, #f1e8d2 60%, #e8dcc0 100%);
  border-radius: 5px 5px 2px 2px;
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
  overflow: hidden;
  padding: 2.25rem 1.5rem;
}

.tablet-inner::before {
  content: "";
  position: absolute;
  inset: 8px;
  border: 1px solid rgba(163, 122, 58, 0.18);
  border-radius: 3px;
  pointer-events: none;
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
  border-top: 10px solid rgba(163, 122, 58, 0.4);
}

.tablet-bottom-ornament {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(163, 122, 58, 0.3),
    transparent
  );
}

.tablet-body {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 1.1rem;
  flex: 1;
  z-index: 1;
}

.tablet-header {
  font-family: "Noto Serif SC", serif;
  font-size: 0.78rem;
  color: var(--color-gold);
  letter-spacing: 0.4em;
  border-bottom: 1px solid rgba(163, 122, 58, 0.22);
  padding-bottom: 0.6rem;
  font-weight: 500;
}

.tablet-center-char {
  font-family: "Ma Shan Zheng", "STKaiti", cursive;
  font-size: 3.6rem;
  color: var(--color-cinnabar);
  line-height: 1.2;
  text-shadow: 0 1px 0 rgba(255, 255, 255, 0.5);
  animation: tabletGlow 5s ease-in-out infinite;
  font-weight: 400;
}

@keyframes tabletGlow {
  0%, 100% {
    text-shadow:
      0 1px 0 rgba(255, 255, 255, 0.5),
      0 0 18px rgba(154, 75, 59, 0.12);
  }
  50% {
    text-shadow:
      0 1px 0 rgba(255, 255, 255, 0.5),
      0 0 28px rgba(154, 75, 59, 0.22);
  }
}

.tablet-sub {
  font-family: "Noto Serif SC", serif;
  font-size: 0.88rem;
  color: var(--color-ink-soft);
  letter-spacing: 0.45em;
  font-weight: 400;
}

.tablet-light {
  position: absolute;
  top: 32%;
  left: 50%;
  transform: translateX(-50%);
  width: 130px;
  height: 130px;
  background: radial-gradient(
    circle,
    rgba(200, 165, 102, 0.18) 0%,
    transparent 70%
  );
  border-radius: 50%;
  animation: tabletLightPulse 5s ease-in-out infinite;
  pointer-events: none;
}

@keyframes tabletLightPulse {
  0%, 100% {
    opacity: 0.5;
    transform: translateX(-50%) scale(1);
  }
  50% {
    opacity: 0.85;
    transform: translateX(-50%) scale(1.25);
  }
}

/* 牌位名号 */
.tablet-title {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.45rem;
  margin-top: 1.4rem;
}

.title-name {
  font-family: "Ma Shan Zheng", "STKaiti", cursive;
  font-size: 1.85rem;
  color: var(--color-ink);
  letter-spacing: 0.55em;
  font-weight: 400;
}

.title-dates {
  font-size: 0.72rem;
  color: var(--color-ink-mute);
  letter-spacing: 0.25em;
  font-style: italic;
  opacity: 0.75;
}

/* 名句区 */
.quote-section {
  max-width: 560px;
  margin: 0.5rem auto 0;
  min-height: 110px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.quote-card {
  text-align: center;
  padding: 1.25rem 1.5rem;
  position: relative;
}

.quote-quote-mark {
  font-family: "Ma Shan Zheng", "STKaiti", serif;
  font-size: 1.5rem;
  color: rgba(163, 122, 58, 0.35);
  line-height: 1;
  display: inline-block;
  vertical-align: top;
}

.quote-quote-mark.end {
  margin-left: 0.2rem;
}

.quote-text {
  font-family: "Ma Shan Zheng", "STKaiti", cursive;
  font-size: 1.4rem;
  color: var(--color-ink);
  letter-spacing: 0.18em;
  padding: 0.4rem 0.4rem;
  line-height: 1.85;
  display: inline;
  font-weight: 400;
}

.quote-source {
  display: block;
  margin-top: 0.85rem;
  font-family: "Noto Serif SC", serif;
  font-size: 0.78rem;
  color: var(--color-ink-mute);
  letter-spacing: 0.15em;
  font-style: italic;
}

/* 名句过渡 */
.quote-enter-active {
  transition: all 0.7s ease;
}
.quote-leave-active {
  transition: all 0.5s ease;
}
.quote-enter-from {
  opacity: 0;
  transform: translateY(8px);
  filter: blur(2px);
}
.quote-leave-to {
  opacity: 0;
  transform: translateY(-8px);
  filter: blur(2px);
}

/* 分隔线 */
.section-divider {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.6rem;
  padding: 1.75rem 0;
}

.divider-dot {
  width: 3px;
  height: 3px;
  border-radius: 50%;
  background: rgba(163, 122, 58, 0.4);
}

.divider-line {
  flex: 1;
  max-width: 110px;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(163, 122, 58, 0.28),
    transparent
  );
}

.divider-ornament {
  color: rgba(154, 75, 59, 0.45);
  font-size: 0.72rem;
}

/* 页脚 */
.shrine-footer {
  text-align: center;
  padding: 3rem 0 2.5rem;
}

.footer-line {
  width: 110px;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(163, 122, 58, 0.28),
    transparent
  );
  margin: 0 auto 1.5rem;
}

.footer-text {
  font-family: "Ma Shan Zheng", "STKaiti", cursive;
  font-size: 0.88rem;
  color: var(--color-ink-soft);
  letter-spacing: 0.25em;
  font-weight: 400;
}

.footer-sub {
  font-size: 0.68rem;
  color: var(--color-ink-mute);
  margin-top: 0.6rem;
  letter-spacing: 0.15em;
  opacity: 0.7;
  font-style: italic;
}

/* 响应式 */
@media (max-width: 640px) {
  .shrine-content {
    padding: 1.25rem 1rem;
  }

  .shrine-title {
    font-size: 2.3rem;
    letter-spacing: 0.25em;
  }

  .hall-frame {
    padding: 2rem 1rem 1.75rem;
  }

  .tablet-inner {
    width: 188px;
    min-height: 240px;
    padding: 1.75rem 1rem;
  }

  .tablet-center-char {
    font-size: 2.8rem;
  }

  .title-name {
    font-size: 1.55rem;
  }

  .quote-text {
    font-size: 1.18rem;
  }
}
</style>