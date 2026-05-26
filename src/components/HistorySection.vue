<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  records: {
    type: Array,
    default: () => []
  }
})

const expandedId = ref(null)
const showAll = ref(false)

const displayedRecords = computed(() => {
  if (showAll.value) return props.records
  return props.records.slice(0, 6)
})

const totalCount = computed(() => props.records.length)

const offeringStats = computed(() => {
  const stats = {}
  props.records.forEach(r => {
    if (r.offering) {
      stats[r.offering.id] = (stats[r.offering.id] || 0) + 1
    }
  })
  return stats
})

function toggleExpand(id) {
  expandedId.value = expandedId.value === id ? null : id
}

function getRelativeTime(timestamp) {
  const diff = Date.now() - timestamp
  const minutes = Math.floor(diff / 60000)
  const hours = Math.floor(diff / 3600000)
  const days = Math.floor(diff / 86400000)

  if (minutes < 1) return '刚刚'
  if (minutes < 60) return `${minutes}分钟前`
  if (hours < 24) return `${hours}小时前`
  if (days < 7) return `${days}天前`
  return new Date(timestamp).toLocaleDateString('zh-CN')
}
</script>

<template>
  <section class="history-section">
    <div class="history-header">
      <span class="header-line"></span>
      <h2>祭拜记录</h2>
      <span class="header-line"></span>
    </div>

    <!-- 统计概览 -->
    <div class="stats-overview" v-if="totalCount > 0">
      <div class="stat-card">
        <span class="stat-number">{{ totalCount }}</span>
        <span class="stat-label">累计祭拜</span>
      </div>
      <span class="stat-divider"></span>
      <div class="stat-card recent">
        <span class="stat-number">
          {{ records.length > 0 ? getRelativeTime(records[0].timestamp) : '-' }}
        </span>
        <span class="stat-label">最近祭拜</span>
      </div>
      <span class="stat-divider" v-if="Object.keys(offeringStats).length"></span>
      <div class="offering-stats">
        <div
          v-for="(count, id) in offeringStats"
          :key="id"
          class="mini-stat"
          :title="`${records.find(r => r.offering?.id === id)?.offering?.name || ''} · ${count}`"
        >
          <span class="mini-stat-icon">
            {{ records.find(r => r.offering?.id === id)?.offering?.icon }}
          </span>
          <span class="mini-stat-count">×{{ count }}</span>
        </div>
      </div>
    </div>

    <!-- 空状态 -->
    <div v-if="totalCount === 0" class="empty-state">
      <div class="empty-icon">📜</div>
      <p class="empty-text">尚无祭拜记录</p>
      <p class="empty-sub">留下您对武侯的第一份敬意</p>
    </div>

    <!-- 记录列表 -->
    <div v-else class="records-list">
      <TransitionGroup name="record">
        <div
          v-for="record in displayedRecords"
          :key="record.id"
          class="record-card"
          :class="{ expanded: expandedId === record.id }"
          @click="toggleExpand(record.id)"
        >
          <div class="record-main">
            <span class="record-offering-icon">{{ record.offering?.icon }}</span>
            <div class="record-info">
              <span class="record-offering-name">{{ record.offering?.name }}</span>
              <span class="record-wish-preview">{{ record.wish }}</span>
            </div>
            <span class="record-time">{{ getRelativeTime(record.timestamp) }}</span>
            <span class="record-arrow">
              <svg viewBox="0 0 12 12" width="10" height="10">
                <path d="M3 4.5 L6 7.5 L9 4.5" stroke="currentColor" stroke-width="1.2" fill="none" stroke-linecap="round" stroke-linejoin="round"/>
              </svg>
            </span>
          </div>
          <Transition name="expand">
            <div v-if="expandedId === record.id" class="record-detail">
              <div class="detail-divider"></div>
              <div class="detail-wish">
                <span class="detail-quote">「</span>
                {{ record.wish }}
                <span class="detail-quote">」</span>
              </div>
              <div class="detail-time">
                <svg viewBox="0 0 12 12" width="10" height="10" style="vertical-align: -1px; margin-right: 4px;">
                  <circle cx="6" cy="6" r="4.5" stroke="currentColor" stroke-width="0.8" fill="none"/>
                  <path d="M6 3.5 V6 L7.8 7.2" stroke="currentColor" stroke-width="0.8" stroke-linecap="round" fill="none"/>
                </svg>
                {{ record.time }}
              </div>
            </div>
          </Transition>
        </div>
      </TransitionGroup>

      <!-- 显示更多 -->
      <button
        v-if="totalCount > 6"
        class="show-more-btn"
        @click="showAll = !showAll"
      >
        {{ showAll ? '收起记录' : `查看全部 ${totalCount} 条记录` }}
      </button>
    </div>
  </section>
</template>

<style scoped>
.history-section {
  padding: 1.5rem 0;
  position: relative;
}

.history-header {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1.5rem;
  margin-bottom: 1.5rem;
}

.history-header h2 {
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

/* 统计概览 */
.stats-overview {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 1.25rem;
  padding: 1.1rem 1.5rem;
  background: rgba(255, 252, 244, 0.6);
  border: 1px solid var(--color-border);
  border-radius: 6px;
  margin-bottom: 1.5rem;
  backdrop-filter: blur(2px);
  box-shadow: 0 1px 2px rgba(60, 50, 35, 0.03);
  position: relative;
}

.stats-overview::before {
  content: '';
  position: absolute;
  inset: 4px;
  border: 1px solid rgba(163, 122, 58, 0.08);
  border-radius: 4px;
  pointer-events: none;
}

.stat-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.3rem;
  position: relative;
  z-index: 1;
}

.stat-number {
  font-family: 'Noto Serif SC', serif;
  font-size: 1.15rem;
  color: var(--color-cinnabar);
  font-weight: 600;
  letter-spacing: 0.05em;
}

.stat-card.recent .stat-number {
  font-size: 0.9rem;
  color: var(--color-ink);
  font-weight: 500;
}

.stat-label {
  font-size: 0.72rem;
  color: var(--color-ink-mute);
  letter-spacing: 0.18em;
  font-weight: 300;
}

.stat-divider {
  width: 1px;
  height: 26px;
  background: linear-gradient(
    180deg,
    transparent,
    rgba(163, 122, 58, 0.3),
    transparent
  );
  position: relative;
  z-index: 1;
}

.offering-stats {
  display: flex;
  gap: 0.7rem;
  position: relative;
  z-index: 1;
  flex-wrap: wrap;
  justify-content: center;
}

.mini-stat {
  display: flex;
  align-items: center;
  gap: 0.25rem;
  padding: 0.18rem 0.5rem;
  background: rgba(255, 250, 238, 0.7);
  border: 1px solid rgba(163, 122, 58, 0.18);
  border-radius: 16px;
  transition: all 0.3s ease;
}

.mini-stat:hover {
  border-color: rgba(163, 122, 58, 0.4);
  background: rgba(255, 250, 238, 1);
}

.mini-stat-icon {
  font-size: 0.95rem;
  filter: grayscale(0.2);
}

.mini-stat-count {
  font-size: 0.72rem;
  color: var(--color-ink-soft);
  font-weight: 500;
  letter-spacing: 0.05em;
}

/* 空状态 */
.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.7rem;
  padding: 3rem 2rem;
  background: rgba(255, 252, 244, 0.4);
  border: 1px dashed rgba(163, 122, 58, 0.2);
  border-radius: 6px;
}

.empty-icon {
  font-size: 2.4rem;
  opacity: 0.4;
  filter: grayscale(0.3);
}

.empty-text {
  font-family: 'Ma Shan Zheng', 'STKaiti', cursive;
  font-size: 1.05rem;
  color: var(--color-ink-soft);
  letter-spacing: 0.2em;
  font-weight: 400;
}

.empty-sub {
  font-size: 0.78rem;
  color: var(--color-ink-mute);
  letter-spacing: 0.1em;
  opacity: 0.75;
}

/* 记录列表 */
.records-list {
  display: flex;
  flex-direction: column;
  gap: 0.55rem;
}

.record-card {
  background: rgba(255, 252, 244, 0.55);
  border: 1px solid var(--color-border);
  border-radius: 5px;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.25, 0.8, 0.3, 1);
  overflow: hidden;
  position: relative;
  backdrop-filter: blur(2px);
}

.record-card::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  width: 2px;
  background: var(--color-cinnabar);
  transform: scaleY(0);
  transform-origin: center;
  transition: transform 0.4s ease;
  opacity: 0.6;
}

.record-card:hover {
  border-color: rgba(163, 122, 58, 0.32);
  background: rgba(255, 252, 244, 0.8);
  box-shadow: 0 4px 12px rgba(60, 50, 35, 0.05);
}

.record-card.expanded {
  border-color: rgba(154, 75, 59, 0.4);
  background: rgba(255, 250, 238, 0.85);
}

.record-card.expanded::before {
  transform: scaleY(1);
}

.record-main {
  display: flex;
  align-items: center;
  gap: 0.85rem;
  padding: 0.85rem 1.1rem;
}

.record-offering-icon {
  font-size: 1.35rem;
  flex-shrink: 0;
  filter: grayscale(0.15);
}

.record-info {
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  gap: 0.18rem;
}

.record-offering-name {
  font-family: 'Noto Serif SC', serif;
  font-size: 0.85rem;
  color: var(--color-ink);
  font-weight: 500;
  letter-spacing: 0.1em;
}

.record-wish-preview {
  font-size: 0.74rem;
  color: var(--color-ink-mute);
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  letter-spacing: 0.05em;
}

.record-time {
  font-size: 0.7rem;
  color: var(--color-ink-mute);
  white-space: nowrap;
  opacity: 0.75;
  letter-spacing: 0.05em;
}

.record-arrow {
  color: var(--color-ink-mute);
  opacity: 0.4;
  transition: transform 0.4s ease, opacity 0.3s ease;
  display: flex;
}

.record-card:hover .record-arrow {
  opacity: 0.8;
}

.record-card.expanded .record-arrow {
  transform: rotate(180deg);
  opacity: 0.9;
  color: var(--color-cinnabar);
}

/* 详情展开 */
.record-detail {
  padding: 0 1.1rem 1rem;
  overflow: hidden;
}

.detail-divider {
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(163, 122, 58, 0.22),
    transparent
  );
  margin: 0 -0.2rem 0.7rem;
}

.detail-wish {
  font-family: 'Ma Shan Zheng', 'STKaiti', cursive;
  font-size: 1.05rem;
  color: var(--color-cinnabar);
  padding: 0.3rem 0 0.6rem;
  letter-spacing: 0.12em;
  line-height: 1.7;
  font-weight: 400;
}

.detail-quote {
  color: rgba(163, 122, 58, 0.5);
  font-size: 0.95rem;
  margin: 0 0.15em;
}

.detail-time {
  font-size: 0.72rem;
  color: var(--color-ink-mute);
  letter-spacing: 0.08em;
  opacity: 0.8;
  display: inline-flex;
  align-items: center;
}

/* 显示更多 */
.show-more-btn {
  display: block;
  margin: 1.25rem auto 0;
  padding: 0.5rem 1.6rem;
  background: rgba(255, 252, 244, 0.5);
  border: 1px solid rgba(163, 122, 58, 0.25);
  border-radius: 22px;
  color: var(--color-ink-soft);
  font-family: 'Noto Serif SC', serif;
  font-size: 0.78rem;
  cursor: pointer;
  transition: all 0.35s ease;
  letter-spacing: 0.15em;
  font-weight: 400;
}

.show-more-btn:hover {
  border-color: var(--color-gold);
  color: var(--color-cinnabar);
  background: rgba(255, 250, 238, 0.85);
  transform: translateY(-1px);
  box-shadow: 0 4px 10px rgba(60, 50, 35, 0.05);
}

/* 过渡动画 */
.record-enter-active {
  transition: all 0.55s cubic-bezier(0.25, 0.8, 0.3, 1);
}
.record-leave-active {
  transition: all 0.35s ease;
}
.record-enter-from {
  opacity: 0;
  transform: translateY(-12px);
}
.record-leave-to {
  opacity: 0;
  transform: translateX(20px);
}

.expand-enter-active,
.expand-leave-active {
  transition: all 0.4s ease;
  overflow: hidden;
}
.expand-enter-from,
.expand-leave-to {
  opacity: 0;
  max-height: 0;
  transform: translateY(-4px);
}
.expand-enter-to,
.expand-leave-from {
  opacity: 1;
  max-height: 200px;
  transform: translateY(0);
}

@media (max-width: 640px) {
  .history-header h2 {
    font-size: 1.55rem;
    letter-spacing: 0.25em;
  }
  .header-line { width: 40px; }
  .stats-overview {
    gap: 0.85rem;
    padding: 0.9rem 1rem;
  }
  .stat-divider { height: 22px; }
  .record-main {
    padding: 0.7rem 0.9rem;
    gap: 0.65rem;
  }
  .record-offering-icon { font-size: 1.2rem; }
}
</style>