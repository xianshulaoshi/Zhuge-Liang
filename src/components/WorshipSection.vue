<script setup>
import { ref } from 'vue'

const emit = defineEmits(['worship'])

const isWorshipping = ref(false)
const showEffect = ref(false)
const selectedOffering = ref('')
const customWish = ref('')
const worshipCount = ref(0)

const offerings = [
  { id: 'incense', name: '清香', icon: '🪵', desc: '三炷清香敬武侯' },
  { id: 'wine', name: '浊酒', icon: '🍶', desc: '一壶浊酒祭英魂' },
  { id: 'flower', name: '素花', icon: '🌸', desc: '素花一束寄哀思' },
  { id: 'lantern', name: '明灯', icon: '🏮', desc: '明灯一盏照千秋' },
]

const wishes = [
  '愿武侯精神永存',
  '鞠躬尽瘁，死而后已',
  '非淡泊无以明志，非宁静无以致远',
  '出师一表真名世，千载谁堪伯仲间',
]

function selectOffering(id) {
  selectedOffering.value = selectedOffering.value === id ? '' : id
}

function selectWish(wish) {
  customWish.value = wish
}

async function performWorship() {
  if (!selectedOffering.value || isWorshipping.value) return

  isWorshipping.value = true
  showEffect.value = true

  await new Promise(resolve => setTimeout(resolve, 2800))

  const record = {
    id: Date.now(),
    offering: offerings.find(o => o.id === selectedOffering.value),
    wish: customWish.value || '敬仰武侯',
    time: new Date().toLocaleString('zh-CN'),
    timestamp: Date.now()
  }

  emit('worship', record)
  worshipCount.value++

  setTimeout(() => {
    isWorshipping.value = false
    showEffect.value = false
    selectedOffering.value = ''
    customWish.value = ''
  }, 800)
}
</script>

<template>
  <section class="worship-section">
    <!-- 祭拜标题 -->
    <div class="worship-header">
      <span class="header-line"></span>
      <h2>虔诚祭拜</h2>
      <span class="header-line"></span>
    </div>
    <p class="worship-subtitle">择一供品&nbsp;·&nbsp;书一心愿&nbsp;·&nbsp;敬拜武侯</p>

    <!-- 供品选择 -->
    <div class="offerings-grid">
      <div
        v-for="offering in offerings"
        :key="offering.id"
        class="offering-card"
        :class="{ selected: selectedOffering === offering.id }"
        @click="selectOffering(offering.id)"
      >
        <span class="offering-icon">{{ offering.icon }}</span>
        <span class="offering-name">{{ offering.name }}</span>
        <span class="offering-desc">{{ offering.desc }}</span>
        <span class="offering-check">
          <svg viewBox="0 0 16 16" width="10" height="10">
            <path d="M3 8 L7 12 L13 4" stroke="currentColor" stroke-width="1.5" fill="none" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
        </span>
      </div>
    </div>

    <!-- 心愿输入 -->
    <div class="wish-section">
      <label class="wish-label">
        <span class="wish-label-mark"></span>
        寄语心愿
        <span class="wish-label-mark"></span>
      </label>
      <div class="wish-presets">
        <span
          v-for="wish in wishes"
          :key="wish"
          class="wish-preset"
          :class="{ active: customWish === wish }"
          @click="selectWish(wish)"
        >{{ wish }}</span>
      </div>
      <textarea
        v-model="customWish"
        class="wish-input"
        placeholder="写下您对武侯的敬仰之心⋯"
        rows="2"
        maxlength="100"
      ></textarea>
      <div class="wish-counter">{{ customWish.length }} / 100</div>
    </div>

    <!-- 祭拜按钮 -->
    <button
      class="worship-btn"
      :class="{ active: isWorshipping, disabled: !selectedOffering || isWorshipping }"
      :disabled="!selectedOffering || isWorshipping"
      @click="performWorship"
    >
      <span class="btn-flourish left">❖</span>
      <span v-if="!isWorshipping" class="btn-text">虔诚祭拜</span>
      <span v-else class="btn-text worshipping">祭拜中⋯</span>
      <span class="btn-flourish right">❖</span>
    </button>

    <!-- 祭拜特效 -->
    <Transition name="fade">
      <div v-if="showEffect" class="worship-effect">
        <div class="effect-particles">
          <span v-for="i in 14" :key="i" class="particle" :style="{
            '--delay': `${i * 0.13}s`,
            '--x': `${(Math.random() - 0.5) * 240}px`,
          }"></span>
        </div>
        <div class="effect-text">
          <span class="effect-icon">{{ offerings.find(o => o.id === selectedOffering)?.icon }}</span>
          <span class="effect-wish">{{ customWish || '敬仰武侯' }}</span>
        </div>
      </div>
    </Transition>
  </section>
</template>

<style scoped>
.worship-section {
  padding: 1.5rem 0;
  position: relative;
}

.worship-header {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1.5rem;
  margin-bottom: 0.6rem;
}

.worship-header h2 {
  font-family: 'Ma Shan Zheng', 'STKaiti', cursive;
  font-size: 1.85rem;
  color: var(--color-ink);
  letter-spacing: 0.4em;
  white-space: nowrap;
  font-weight: 400;
}

.header-line {
  width: 56px;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(163, 122, 58, 0.4),
    transparent
  );
}

.worship-subtitle {
  text-align: center;
  color: var(--color-ink-mute);
  font-size: 0.82rem;
  margin-bottom: 2rem;
  letter-spacing: 0.18em;
  font-weight: 300;
}

/* 供品选择 */
.offerings-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.85rem;
  margin-bottom: 2rem;
  max-width: 480px;
  margin-left: auto;
  margin-right: auto;
}

.offering-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.45rem;
  padding: 1.3rem 0.8rem;
  background: rgba(255, 252, 244, 0.6);
  border: 1px solid var(--color-border);
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.45s cubic-bezier(0.25, 0.8, 0.3, 1);
  position: relative;
  overflow: hidden;
  backdrop-filter: blur(2px);
}

.offering-card::before {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(
    circle at center,
    rgba(200, 165, 102, 0.1) 0%,
    transparent 70%
  );
  opacity: 0;
  transition: opacity 0.45s;
}

.offering-card::after {
  content: '';
  position: absolute;
  inset: 4px;
  border: 1px dashed transparent;
  border-radius: 4px;
  pointer-events: none;
  transition: border-color 0.4s;
}

.offering-card:hover::before,
.offering-card.selected::before {
  opacity: 1;
}

.offering-card:hover {
  border-color: rgba(163, 122, 58, 0.4);
  transform: translateY(-2px);
  box-shadow:
    0 1px 2px rgba(60, 50, 35, 0.04),
    0 8px 18px rgba(60, 50, 35, 0.06);
}

.offering-card.selected {
  border-color: var(--color-gold);
  background: rgba(255, 250, 238, 0.85);
  box-shadow:
    0 0 0 1px rgba(163, 122, 58, 0.15),
    0 6px 16px rgba(163, 122, 58, 0.08);
}

.offering-card.selected::after {
  border-color: rgba(163, 122, 58, 0.25);
}

.offering-icon {
  font-size: 1.85rem;
  filter: grayscale(0.4) opacity(0.85);
  transition: filter 0.4s, transform 0.4s;
}

.offering-card:hover .offering-icon,
.offering-card.selected .offering-icon {
  filter: grayscale(0) opacity(1);
  transform: scale(1.05);
}

.offering-name {
  font-family: 'Noto Serif SC', serif;
  font-size: 0.95rem;
  color: var(--color-ink);
  font-weight: 500;
  letter-spacing: 0.15em;
}

.offering-desc {
  font-size: 0.7rem;
  color: var(--color-ink-mute);
  letter-spacing: 0.08em;
  font-weight: 300;
}

.offering-check {
  position: absolute;
  top: 8px;
  right: 8px;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: var(--color-cinnabar);
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  transform: scale(0.6);
  transition: all 0.35s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.offering-card.selected .offering-check {
  opacity: 1;
  transform: scale(1);
}

/* 心愿输入 */
.wish-section {
  max-width: 480px;
  margin: 0 auto 2rem;
}

.wish-label {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  font-family: 'Noto Serif SC', serif;
  font-size: 0.85rem;
  color: var(--color-ink-soft);
  margin-bottom: 0.9rem;
  letter-spacing: 0.3em;
  font-weight: 500;
}

.wish-label-mark {
  width: 4px;
  height: 4px;
  border-radius: 50%;
  background: rgba(154, 75, 59, 0.4);
}

.wish-presets {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 0.45rem;
  margin-bottom: 1rem;
}

.wish-preset {
  font-size: 0.74rem;
  padding: 0.34rem 0.85rem;
  background: rgba(255, 252, 244, 0.5);
  border: 1px solid rgba(163, 122, 58, 0.18);
  border-radius: 24px;
  color: var(--color-ink-soft);
  cursor: pointer;
  transition: all 0.35s ease;
  letter-spacing: 0.05em;
  font-weight: 400;
}

.wish-preset:hover {
  background: rgba(255, 250, 238, 0.85);
  border-color: rgba(163, 122, 58, 0.4);
  color: var(--color-gold);
  transform: translateY(-1px);
}

.wish-preset.active {
  background: rgba(154, 75, 59, 0.06);
  border-color: rgba(154, 75, 59, 0.4);
  color: var(--color-cinnabar);
}

.wish-input {
  width: 100%;
  background: rgba(255, 252, 244, 0.55);
  border: 1px solid var(--color-border);
  border-radius: 4px;
  padding: 0.85rem 1rem;
  color: var(--color-ink);
  font-family: 'Noto Serif SC', serif;
  font-size: 0.92rem;
  resize: none;
  outline: none;
  transition: all 0.3s ease;
  box-sizing: border-box;
  line-height: 1.7;
}

.wish-input:focus {
  border-color: rgba(163, 122, 58, 0.5);
  background: rgba(255, 252, 244, 0.85);
  box-shadow: 0 0 0 3px rgba(163, 122, 58, 0.06);
}

.wish-input::placeholder {
  color: var(--color-ink-mute);
  opacity: 0.6;
  font-style: italic;
}

.wish-counter {
  text-align: right;
  font-size: 0.7rem;
  color: var(--color-ink-mute);
  margin-top: 0.4rem;
  opacity: 0.6;
  letter-spacing: 0.05em;
}

/* 祭拜按钮 */
.worship-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.85rem;
  margin: 0 auto;
  padding: 0.85rem 2.6rem;
  background: linear-gradient(
    180deg,
    rgba(255, 250, 238, 0.9),
    rgba(244, 233, 210, 0.9)
  );
  border: 1px solid rgba(163, 122, 58, 0.45);
  border-radius: 4px;
  color: var(--color-cinnabar);
  font-family: 'Ma Shan Zheng', 'STKaiti', cursive;
  font-size: 1.2rem;
  letter-spacing: 0.35em;
  cursor: pointer;
  transition: all 0.45s cubic-bezier(0.25, 0.8, 0.3, 1);
  position: relative;
  overflow: hidden;
  font-weight: 400;
  box-shadow:
    inset 0 1px 0 rgba(255, 255, 255, 0.5),
    0 1px 2px rgba(60, 50, 35, 0.04),
    0 6px 14px rgba(60, 50, 35, 0.06);
}

.worship-btn::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(
    135deg,
    transparent 35%,
    rgba(255, 255, 255, 0.4) 50%,
    transparent 65%
  );
  transform: translateX(-110%);
  transition: transform 0.8s ease;
  pointer-events: none;
}

.btn-flourish {
  font-size: 0.7rem;
  color: rgba(163, 122, 58, 0.55);
  letter-spacing: 0;
  transition: transform 0.5s ease;
}

.worship-btn:hover:not(.disabled) .btn-flourish.left {
  transform: rotate(-90deg);
}
.worship-btn:hover:not(.disabled) .btn-flourish.right {
  transform: rotate(90deg);
}

.worship-btn:hover:not(.disabled)::after {
  transform: translateX(110%);
}

.worship-btn:hover:not(.disabled) {
  background: linear-gradient(
    180deg,
    rgba(255, 250, 232, 0.95),
    rgba(244, 230, 200, 0.95)
  );
  border-color: var(--color-cinnabar);
  box-shadow:
    inset 0 1px 0 rgba(255, 255, 255, 0.6),
    0 2px 4px rgba(60, 50, 35, 0.06),
    0 10px 22px rgba(154, 75, 59, 0.1);
  transform: translateY(-1px);
}

.worship-btn.disabled {
  opacity: 0.45;
  cursor: not-allowed;
  filter: saturate(0.4);
}

.worship-btn.active {
  animation: worshipPulse 1.4s ease-in-out infinite;
}

@keyframes worshipPulse {
  0%, 100% {
    box-shadow:
      inset 0 1px 0 rgba(255, 255, 255, 0.5),
      0 0 0 0 rgba(154, 75, 59, 0.18);
  }
  50% {
    box-shadow:
      inset 0 1px 0 rgba(255, 255, 255, 0.5),
      0 0 0 8px rgba(154, 75, 59, 0);
  }
}

.btn-text.worshipping {
  animation: textFade 1.4s ease-in-out infinite;
}

@keyframes textFade {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.55; }
}

/* 祭拜特效 */
.worship-effect {
  position: fixed;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 100;
  pointer-events: none;
  background: radial-gradient(
    ellipse at center,
    rgba(244, 239, 230, 0.5) 0%,
    rgba(244, 239, 230, 0) 60%
  );
  backdrop-filter: blur(2px);
}

.effect-particles {
  position: absolute;
  inset: 0;
}

.particle {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 4px;
  height: 4px;
  background: var(--color-gold-soft);
  border-radius: 50%;
  box-shadow: 0 0 10px rgba(200, 165, 102, 0.7);
  animation: particleRise 2.2s var(--delay) ease-out forwards;
  opacity: 0;
}

@keyframes particleRise {
  0% {
    opacity: 0;
    transform: translate(-50%, -50%) scale(0);
  }
  20% {
    opacity: 1;
    transform: translate(calc(-50% + var(--x) * 0.3), calc(-50% - 60px)) scale(1);
  }
  100% {
    opacity: 0;
    transform: translate(calc(-50% + var(--x)), calc(-50% - 280px)) scale(0.3);
  }
}

.effect-text {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  animation: effectAppear 2.5s ease-out forwards;
}

.effect-icon {
  font-size: 3.5rem;
  animation: iconFloat 1.8s ease-in-out infinite;
  filter: drop-shadow(0 4px 12px rgba(60, 50, 35, 0.15));
}

@keyframes iconFloat {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-12px); }
}

.effect-wish {
  font-family: 'Ma Shan Zheng', 'STKaiti', cursive;
  font-size: 1.4rem;
  color: var(--color-cinnabar);
  letter-spacing: 0.22em;
  text-shadow:
    0 1px 0 rgba(255, 255, 255, 0.6),
    0 0 18px rgba(154, 75, 59, 0.25);
  font-weight: 400;
}

@keyframes effectAppear {
  0% { opacity: 0; transform: scale(0.85); }
  20% { opacity: 1; transform: scale(1); }
  80% { opacity: 1; transform: scale(1); }
  100% { opacity: 0; transform: scale(1.08); }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.6s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

@media (max-width: 640px) {
  .worship-header h2 {
    font-size: 1.55rem;
    letter-spacing: 0.25em;
  }
  .header-line { width: 40px; }
  .offerings-grid { gap: 0.65rem; }
  .offering-card { padding: 1rem 0.6rem; }
  .offering-icon { font-size: 1.6rem; }
  .worship-btn {
    font-size: 1.05rem;
    padding: 0.75rem 1.8rem;
    letter-spacing: 0.25em;
  }
}
</style>