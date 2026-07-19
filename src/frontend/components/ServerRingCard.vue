<template>
  <router-link :to="to" class="server-card server-card-ring" :data-region="regionCode">
    <div class="server-card-ring-header">
      <div class="server-card-ring-main">
        <div class="server-card-ring-title-row">
          <span v-if="regionCode && regionCode !== 'xx'" class="server-card-ring-flag-wrap">
            <img class="flag-img server-card-ring-flag" :src="getPublicAssetUrl('flags/' + regionCode + '.svg')" :alt="regionCode">
          </span>
          <span v-else class="server-card-ring-flag-wrap">
            <span class="flag-fallback server-card-ring-flag-fallback">🏳️</span>
          </span>
          <span class="server-card-ring-name">{{ server.name }}</span>
        </div>
        <div class="server-card-ring-meta">
          <span class="server-card-ring-os">
            <OsIcon :os="server.os" />
            <span>{{ osName }}</span>
          </span>
          <span class="server-card-ring-dot">•</span>
          <span class="server-card-ring-uptime">{{ uptimeText }}</span>
        </div>
      </div>
      <div class="server-card-ring-actions">
        <span class="status-label" :style="{ color: statusColor, borderColor: statusColor }">{{ statusText }}</span>
      </div>
    </div>

    <div class="server-card-ring-divider"></div>

    <div class="server-card-ring-metrics">
      <div class="metric-ring-item">
        <div class="metric-ring-chart" :style="getRingStyle(cpuPercent, 'var(--accent-cyan)')">
          <span class="metric-ring-track"></span>
          <span class="metric-ring-progress"></span>
          <span class="metric-ring-center">{{ roundedPercent(cpuPercent) }}%</span>
        </div>
        <div class="metric-ring-label">CPU</div>
        <div class="metric-ring-subtext">{{ cpuPercent.toFixed(1) }}%</div>
      </div>

      <div class="metric-ring-item">
        <div class="metric-ring-chart" :style="getRingStyle(ramPercent, 'var(--accent-purple)')">
          <span class="metric-ring-track"></span>
          <span class="metric-ring-progress"></span>
          <span class="metric-ring-center">{{ roundedPercent(ramPercent) }}%</span>
        </div>
        <div class="metric-ring-label">RAM</div>
        <div class="metric-ring-subtext">{{ ramUsageText }}</div>
      </div>

      <div class="metric-ring-item">
        <div class="metric-ring-chart" :style="getRingStyle(diskPercent, 'var(--accent-green)')">
          <span class="metric-ring-track"></span>
          <span class="metric-ring-progress"></span>
          <span class="metric-ring-center">{{ roundedPercent(diskPercent) }}%</span>
        </div>
        <div class="metric-ring-label">Disk</div>
        <div class="metric-ring-subtext">{{ diskUsageText }}</div>
      </div>
    </div>

    <div class="server-card-network-panel">
      <div class="server-card-network-row">
        <span class="server-card-network-label">{{ trans.networkTraffic }}</span>
        <span class="server-card-network-values">
          <span class="server-card-speed-up">↑ {{ netOutSpeed }}/s</span>
          <span class="server-card-speed-down">↓ {{ netInSpeed }}/s</span>
        </span>
      </div>
      <div class="server-card-network-row">
        <span class="server-card-network-label">{{ trans.totalTraffic }}</span>
        <span class="server-card-network-values server-card-total-values">
          <span>↑ {{ totalTx }}</span>
          <span>↓ {{ totalRx }}</span>
        </span>
      </div>
      <div v-if="sysConfig.show_tf && trafficLimitSummary" class="server-card-limit-section">
        <div class="server-card-limit-header">
          <span>{{ trans.monthlyTraffic }}</span>
          <span>{{ trafficLimitText }} | {{ trafficLimitPercentText }}%</span>
        </div>
        <div class="server-card-limit-bar">
          <div class="server-card-limit-fill" :style="{ width: Math.min(100, trafficUsagePercent) + '%' }"></div>
        </div>
      </div>
      <div class="server-card-ping-row">
        <span class="server-card-ping-chip" v-for="p in pingList" :key="p.label">
          <span class="server-card-ping-label">{{ p.label }}</span>
          <span class="server-card-ping-val" :style="{ color: getPingColor(p.value) }">{{ isPingValid(p.value) ? p.value + 'ms' : trans.timeout }}</span>
        </span>
      </div>
    </div>
  </router-link>
</template>

<script setup>
import { computed } from 'vue'
import OsIcon from './OsIcon.vue'
import { getOSName } from '../utils/osIcon'
import { DEFAULT_SERVER_CARD_CONFIG, useServerCardData } from '../composables/useServerCardData'

const props = defineProps({
  server: {
    type: Object,
    required: true
  },
  sysConfig: {
    type: Object,
    default: () => ({ ...DEFAULT_SERVER_CARD_CONFIG })
  },
  to: {
    type: String,
    default: ''
  }
})

const {
  trans,
  regionCode,
  statusColor,
  statusText,
  cpuPercent,
  ramPercent,
  diskPercent,
  trafficLimitSummary,
  trafficUsagePercent,
  trafficLimitPercentText,
  trafficLimitText,
  netInSpeed,
  netOutSpeed,
  totalRx,
  totalTx,
  uptimeText,
  ramUsageText,
  diskUsageText,
  getRingStyle,
  roundedPercent,
  isPingValid,
  getPingColor,
  pingList,
  getPublicAssetUrl
} = useServerCardData(props)

const osName = computed(() => getOSName(props.server.os))
</script>
