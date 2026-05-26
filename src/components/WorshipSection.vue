<script setup>
import { ref, onMounted } from 'vue'

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

  // 祭拜动画持续
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

  // 重置状态
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
      <div class="header-line"></div>
      <h2>虔诚祭拜</h2>
      <div class="header-line"></div>
    </div>
    <p class="worship-subtitle">择一供品，书一心愿，敬拜武侯</p>

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
      </div>
    </div>

    <!-- 心愿输入 -->
    <div class="wish-section">
      <label class="wish-label">寄语心愿</label>
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
        placeholder="写下您对武侯的敬仰之心..."
        rows="2"
        maxlength="100"
      ></textarea>
    </div>

    <!-- 祭拜按钮 -->
    <button
      class="worship-btn"
      :class="{ active: isWorshipping, disabled: !selectedOffering || isWorshipping }"
      :disabled="!selectedOffering || isWorshipping"
      @click="performWorship"
    >
      <span v-if="!isWorshipping" class="btn-text">虔诚祭拜</span>
      <span v-else class="btn-text worshipping">祭拜中...</span>
    </button>

    <!-- 祭拜特效 -->
    <Transition name="fade">
      <div v-if="showEffect" class="worship-effect">
        <div class="effect-particles">
          <span v-for="i in 12" :key="i" class="particle" :style="{
            '--delay': `${i * 0.15}s`,
            '--x': `${(Math.random() - 0.5) * 200}px`,
            '--angle': `${i * 30}deg`
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
  padding: 2rem 0;
  position: relative;
}

.worship-header {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1.5rem;
  margin-bottom: 0.5rem;
}

.worship-header h2 {
  font-family: 'Ma Shan Zheng', cursive;
  font-size: 2rem;
  color: var(--color-gold);
  letter-spacing: 0.3em;
  white-space: nowrap;
}

.header-line {
  width: 60px;
  height: 1px;
  background: linear-gradient(90deg, transparent, var(--color-gold), transparent);
}

.worship-subtitle {
  text-align: center;
  color: var(--color-text-muted);
  font-size: 0.9rem;
  margin-bottom: 2rem;
  letter-spacing: 0.15em;
}

/* 供品选择 */
.offerings-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  margin-bottom: 2rem;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
}

.offering-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.4rem;
  padding: 1.2rem 0.8rem;
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(212, 175, 55, 0.15);
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.4s ease;
  position: relative;
  overflow: hidden;
}

.offering-card::before {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at center, rgba(212, 175, 55, 0.08) 0%, transparent 70%);
  opacity: 0;
  transition: opacity 0.4s;
}

.offering-card:hover::before,
.offering-card.selected::before {
  opacity: 1;
}

.offering-card:hover {
  border-color: rgba(212, 175, 55, 0.4);
  transform: translateY(-2px);
}

.offering-card.selected {
  border-color: var(--color-gold);
  box-shadow: 0 0 20px rgba(212, 175, 55, 0.15);
}

.offering-icon {
  font-size: 2rem;
  filter: grayscale(0.3);
  transition: filter 0.3s;
}

.offering-card.selected .offering-icon {
  filter: grayscale(0);
}

.offering-name {
  font-family: 'Noto Serif SC', serif;
  font-size: 1rem;
  color: var(--color-text);
  font-weight: 600;
}

.offering-desc {
  font-size: 0.75rem;
  color: var(--color-text-muted);
  letter-spacing: 0.05em;
}

/* 心愿输入 */
.wish-section {
  max-width: 500px;
  margin: 0 auto 2rem;
}

.wish-label {
  display: block;
  font-family: 'Noto Serif SC', serif;
  font-size: 0.9rem;
  color: var(--color-text);
  margin-bottom: 0.8rem;
  letter-spacing: 0.1em;
}

.wish-presets {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.wish-preset {
  font-size: 0.75rem;
  padding: 0.35rem 0.8rem;
  background: rgba(212, 175, 55, 0.08);
  border: 1px solid rgba(212, 175, 55, 0.2);
  border-radius: 20px;
  color: var(--color-text-muted);
  cursor: pointer;
  transition: all 0.3s;
  letter-spacing: 0.05em;
}

.wish-preset:hover,
.wish-preset.active {
  background: rgba(212, 175, 55, 0.15);
  border-color: var(--color-gold);
  color: var(--color-gold);
}

.wish-input {
  width: 100%;
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(212, 175, 55, 0.15);
  border-radius: 6px;
  padding: 0.8rem 1rem;
  color: var(--color-text);
  font-family: 'Noto Serif SC', serif;
  font-size: 0.9rem;
  resize: none;
  outline: none;
  transition: border-color 0.3s;
  box-sizing: border-box;
}

.wish-input:focus {
  border-color: rgba(212, 175, 55, 0.5);
}

.wish-input::placeholder {
  color: rgba(180, 160, 120, 0.4);
}

/* 祭拜按钮 */
.worship-btn {
  display: block;
  margin: 0 auto;
  padding: 0.9rem 3rem;
  background: linear-gradient(135deg, rgba(212, 175, 55, 0.2), rgba(212, 175, 55, 0.1));
  border: 1px solid rgba(212, 175, 55, 0.4);
  border-radius: 6px;
  color: var(--color-gold);
  font-family: 'Ma Shan Zheng', cursive;
  font-size: 1.3rem;
  letter-spacing: 0.3em;
  cursor: pointer;
  transition: all 0.4s;
  position: relative;
  overflow: hidden;
}

.worship-btn::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, transparent 40%, rgba(212, 175, 55, 0.1) 50%, transparent 60%);
  transform: translateX(-100%);
  transition: transform 0.6s;
}

.worship-btn:hover:not(.disabled)::after {
  transform: translateX(100%);
}

.worship-btn:hover:not(.disabled) {
  background: linear-gradient(135deg, rgba(212, 175, 55, 0.3), rgba(212, 175, 55, 0.15));
  box-shadow: 0 0 30px rgba(212, 175, 55, 0.2);
  transform: translateY(-1px);
}

.worship-btn.disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.worship-btn.active {
  animation: worshipPulse 1s ease-in-out infinite;
}

@keyframes worshipPulse {
  0%, 100% { box-shadow: 0 0 20px rgba(212, 175, 55, 0.2); }
  50% { box-shadow: 0 0 40px rgba(212, 175, 55, 0.4); }
}

.btn-text.worshipping {
  animation: textFade 1s ease-in-out infinite;
}

@keyframes textFade {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
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
  background: var(--color-gold);
  border-radius: 50%;
  animation: particleRise 2s var(--delay) ease-out forwards;
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
    transform: translate(calc(-50% + var(--x)), calc(-50% - 250px)) scale(0.3);
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
  font-size: 4rem;
  animation: iconFloat 1.5s ease-in-out infinite;
}

@keyframes iconFloat {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

.effect-wish {
  font-family: 'Ma Shan Zheng', cursive;
  font-size: 1.5rem;
  color: var(--color-gold);
  letter-spacing: 0.2em;
  text-shadow: 0 0 20px rgba(212, 175, 55, 0.5);
}

@keyframes effectAppear {
  0% { opacity: 0; transform: scale(0.8); }
  20% { opacity: 1; transform: scale(1); }
  80% { opacity: 1; transform: scale(1); }
  100% { opacity: 0; transform: scale(1.1); }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>