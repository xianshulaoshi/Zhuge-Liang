<script setup>
import { ref, computed, onMounted } from 'vue'

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

// 供品统计
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

// 格式化时间显示
function formatTime(timeStr) {
  return timeStr
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
  return formatTime(new Date(timestamp).toLocaleString('zh-CN'))
}
</script>

<template>
  <section class="history-section">
    <div class="history-header">
      <div class="header-line"></div>
      <h2>祭拜记录</h2>
      <div class="header-line"></div>
    </div>

    <!-- 统计概览 -->
    <div class="stats-overview" v-if="totalCount > 0">
      <div class="stat-card">
        <span class="stat-number">{{ totalCount }}</span>
        <span class="stat-label">累计祭拜</span>
      </div>
      <div class="stat-card">
        <span class="stat-number">
          {{ records.length > 0 ? getRelativeTime(records[0].timestamp) : '-' }}
        </span>
        <span class="stat-label">最近祭拜</span>
      </div>
      <div class="stat-divider"></div>
      <div class="offering-stats">
        <div v-for="(count, id) in offeringStats" :key="id" class="mini-stat">
          <span class="mini-stat-icon">
            {{ records.find(r => r.offering?.id === id)?.offering?.icon }}
          </span>
          <span class="mini-stat-count">{{ count }}</span>
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
          </div>
          <Transition name="expand">
            <div v-if="expandedId === record.id" class="record-detail">
              <div class="detail-wish">{{ record.wish }}</div>
              <div class="detail-time">{{ record.time }}</div>
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
  padding: 2rem 0;
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

/* 统计概览 */
.stats-overview {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1.5rem;
  padding: 1.2rem;
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(212, 175, 55, 0.1);
  border-radius: 8px;
  margin-bottom: 1.5rem;
}

.stat-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.3rem;
}

.stat-number {
  font-family: 'Noto Serif SC', serif;
  font-size: 1.2rem;
  color: var(--color-gold);
  font-weight: 600;
}

.stat-label {
  font-size: 0.75rem;
  color: var(--color-text-muted);
}

.stat-divider {
  width: 1px;
  height: 30px;
  background: rgba(212, 175, 55, 0.2);
}

.offering-stats {
  display: flex;
  gap: 0.8rem;
}

.mini-stat {
  display: flex;
  align-items: center;
  gap: 0.3rem;
}

.mini-stat-icon {
  font-size: 1rem;
}

.mini-stat-count {
  font-size: 0.8rem;
  color: var(--color-text-muted);
}

/* 空状态 */
.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.8rem;
  padding: 3rem;
}

.empty-icon {
  font-size: 2.5rem;
  opacity: 0.3;
}

.empty-text {
  font-family: 'Noto Serif SC', serif;
  font-size: 1rem;
  color: var(--color-text-muted);
}

.empty-sub {
  font-size: 0.8rem;
  color: rgba(180, 160, 120, 0.4);
}

/* 记录列表 */
.records-list {
  display: flex;
  flex-direction: column;
  gap: 0.6rem;
}

.record-card {
  background: rgba(255, 255, 255, 0.02);
  border: 1px solid rgba(212, 175, 55, 0.08);
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.3s;
  overflow: hidden;
}

.record-card:hover {
  border-color: rgba(212, 175, 55, 0.2);
}

.record-card.expanded {
  border-color: rgba(212, 175, 55, 0.3);
}

.record-main {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  padding: 0.8rem 1rem;
}

.record-offering-icon {
  font-size: 1.3rem;
}

.record-info {
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  gap: 0.2rem;
}

.record-offering-name {
  font-family: 'Noto Serif SC', serif;
  font-size: 0.85rem;
  color: var(--color-text);
}

.record-wish-preview {
  font-size: 0.75rem;
  color: var(--color-text-muted);
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.record-time {
  font-size: 0.7rem;
  color: rgba(180, 160, 120, 0.4);
  white-space: nowrap;
}

/* 详情展开 */
.record-detail {
  padding: 0 1rem 0.8rem;
  border-top: 1px solid rgba(212, 175, 55, 0.08);
}

.detail-wish {
  font-family: 'Ma Shan Zheng', cursive;
  font-size: 1.1rem;
  color: var(--color-gold);
  padding: 0.6rem 0;
  letter-spacing: 0.1em;
}

.detail-time {
  font-size: 0.75rem;
  color: rgba(180, 160, 120, 0.5);
}

/* 显示更多 */
.show-more-btn {
  display: block;
  margin: 1rem auto 0;
  padding: 0.5rem 1.5rem;
  background: transparent;
  border: 1px solid rgba(212, 175, 55, 0.2);
  border-radius: 4px;
  color: var(--color-text-muted);
  font-family: 'Noto Serif SC', serif;
  font-size: 0.8rem;
  cursor: pointer;
  transition: all 0.3s;
  letter-spacing: 0.1em;
}

.show-more-btn:hover {
  border-color: var(--color-gold);
  color: var(--color-gold);
}

/* 过渡动画 */
.record-enter-active {
  transition: all 0.5s ease;
}
.record-leave-active {
  transition: all 0.3s ease;
}
.record-enter-from {
  opacity: 0;
  transform: translateY(-20px);
}
.record-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

.expand-enter-active,
.expand-leave-active {
  transition: all 0.3s;
}
.expand-enter-from,
.expand-leave-to {
  opacity: 0;
  max-height: 0;
}
</style>