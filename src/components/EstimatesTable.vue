<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-50 via-blue-50 to-indigo-100 p-4 sm:p-6 lg:p-8">
    <div class="max-w-7xl mx-auto">
      <!-- Header Section -->
      <div class="mb-8" ref="headerRef">
        <h1 class="text-3xl sm:text-4xl font-bold text-gray-900 mb-2">Financial Dashboard</h1>
        <p class="text-gray-600 text-lg">Track your estimates vs actuals with precision</p>
      </div>

      <!-- Stats Cards -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8" ref="statsCardsRef">
        <!-- Estimated Profit Card -->
        <div class="stats-card bg-white rounded-2xl p-6 shadow-lg border border-gray-100 hover:shadow-xl transition-all duration-300 transform hover:-translate-y-1" ref="estimatedProfitCard">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-sm font-medium text-gray-600 mb-1">Estimated Profit</p>
              <p class="text-2xl font-bold text-blue-600" ref="estimatedProfitValue">€{{ formatNumber(estimatedProfit) }}</p>
              <p class="text-sm text-gray-500 mt-1">{{ estimatedProfitPercentage }}% margin</p>
            </div>
            <div class="w-12 h-12 bg-blue-100 rounded-xl flex items-center justify-center">
              <svg class="w-6 h-6 text-blue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path>
              </svg>
            </div>
          </div>
        </div>

        <!-- Actual Profit Card -->
        <div class="stats-card bg-white rounded-2xl p-6 shadow-lg border border-gray-100 hover:shadow-xl transition-all duration-300 transform hover:-translate-y-1" ref="actualProfitCard">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-sm font-medium text-gray-600 mb-1">Actual Profit</p>
              <p class="text-2xl font-bold text-green-600" ref="actualProfitValue">€{{ formatNumber(actualProfit) }}</p>
              <p class="text-sm text-gray-500 mt-1">{{ actualProfitPercentage }}% margin</p>
            </div>
            <div class="w-12 h-12 bg-green-100 rounded-xl flex items-center justify-center">
              <svg class="w-6 h-6 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1"></path>
              </svg>
            </div>
          </div>
        </div>

        <!-- Total Revenue Card -->
        <div class="stats-card bg-white rounded-2xl p-6 shadow-lg border border-gray-100 hover:shadow-xl transition-all duration-300 transform hover:-translate-y-1" ref="totalRevenueCard">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-sm font-medium text-gray-600 mb-1">Total Revenue</p>
              <p class="text-2xl font-bold text-purple-600" ref="totalRevenueValue">€{{ formatNumber(currentCumulative.estRev + currentCumulative.actRev) }}</p>
              <p class="text-sm text-gray-500 mt-1">Est + Actual</p>
            </div>
            <div class="w-12 h-12 bg-purple-100 rounded-xl flex items-center justify-center">
              <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6"></path>
              </svg>
            </div>
          </div>
        </div>

        <!-- Total Cost Card -->
        <div class="stats-card bg-white rounded-2xl p-6 shadow-lg border border-gray-100 hover:shadow-xl transition-all duration-300 transform hover:-translate-y-1" ref="totalCostCard">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-sm font-medium text-gray-600 mb-1">Total Cost</p>
              <p class="text-2xl font-bold text-orange-600" ref="totalCostValue">€{{ formatNumber(currentCumulative.estCost + currentCumulative.actCost) }}</p>
              <p class="text-sm text-gray-500 mt-1">Est + Actual</p>
            </div>
            <div class="w-12 h-12 bg-orange-100 rounded-xl flex items-center justify-center">
              <svg class="w-6 h-6 text-orange-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 9V7a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2m2 4h10a2 2 0 002-2v-6a2 2 0 00-2-2H9a2 2 0 00-2 2v6a2 2 0 002 2zm7-5a2 2 0 11-4 0 2 2 0 014 0z"></path>
              </svg>
            </div>
          </div>
        </div>
      </div>

      <!-- Main Content Card -->
      <div class="bg-white rounded-3xl shadow-xl border border-gray-100 overflow-hidden" ref="mainCardRef">
        <!-- Tab Navigation -->
        <div class="bg-gray-50/50">
          <div class="flex gap-1 p-4" ref="tabsRef">
            <button
              v-for="tab in tabs"
              :key="tab.id"
              class="tab-button px-6 py-3 rounded-xl text-sm font-medium transition-all duration-200 relative border-none focus:outline-none"
              :class="
                activeTab === tab.id
                  ? 'bg-white text-blue-600'
                  : 'text-gray-600 hover:text-gray-900 hover:bg-gray-100'
              "
              @click="switchTab(tab.id)"
            >
              {{ tab.label }}
              <div v-if="activeTab === tab.id" class="absolute bottom-0 left-0 right-0 h-0.5 bg-blue-600 rounded-full"></div>
            </button>
          </div>
        </div>

        <!-- Search and Controls -->
        <div class="p-6 border-b border-gray-100" ref="searchRef">
          <div class="relative max-w-md">
            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
              <svg class="h-5 w-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
              </svg>
            </div>
            <input
              v-model="filterText"
              type="text"
              placeholder="Search items..."
              class="block w-full pl-10 pr-3 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200 text-sm"
            />
          </div>
        </div>

        <!-- Table -->
        <div class="overflow-x-auto" ref="tableRef">
          <table class="w-full">
            <thead class="bg-gray-50/50" ref="tableHeaderRef">
              <tr>
                <th class="px-6 py-4 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">Item Line</th>
                <th class="px-6 py-4 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">Est Cost</th>
                <th class="px-6 py-4 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">Act Cost</th>
                <th class="px-6 py-4 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">Est Rev</th>
                <th class="px-6 py-4 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">Act Rev</th>
                <th class="px-6 py-4 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">Profit</th>
                <th class="px-6 py-4 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">Margin %</th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-100" ref="tableBodyRef">
                              <tr
                  v-for="(item, index) in filteredItems"
                  :key="index"
                  class="table-row hover:bg-gray-50/50 transition-colors duration-200"
                >
                <td class="px-6 py-4">
                  <div class="flex items-center">
                    <div class="w-2 h-2 bg-blue-500 rounded-full mr-3"></div>
                    <span class="font-medium text-gray-900">{{ item.name }}</span>
                  </div>
                </td>
                <td class="px-6 py-4">
                  <span class="text-gray-900 font-medium" :class="{ 'opacity-40': activeTab === 'actuals' }">
                    €{{ activeTab !== 'actuals' ? formatNumber(item.estCost) : '0' }}
                  </span>
                </td>
                <td class="px-6 py-4">
                  <span class="text-gray-900 font-medium" :class="{ 'opacity-40': activeTab === 'estimates' }">
                    €{{ activeTab !== 'estimates' ? formatNumber(item.actCost) : '0' }}
                  </span>
                </td>
                <td class="px-6 py-4">
                  <span class="text-gray-900 font-medium" :class="{ 'opacity-40': activeTab === 'actuals' }">
                    €{{ activeTab !== 'actuals' ? formatNumber(item.estRev) : '0' }}
                  </span>
                </td>
                <td class="px-6 py-4">
                  <span class="text-gray-900 font-medium" :class="{ 'opacity-40': activeTab === 'estimates' }">
                    €{{ activeTab !== 'estimates' ? formatNumber(item.actRev) : '0' }}
                  </span>
                </td>
                <td class="px-6 py-4">
                  <div class="flex items-center">
                    <span class="font-semibold" :class="getProfitClass(getItemProfit(item))">
                      €{{ formatNumber(getItemProfit(item)) }}
                    </span>
                    <div class="ml-2 w-2 h-2 rounded-full" :class="getItemProfit(item) >= 0 ? 'bg-green-500' : 'bg-red-500'"></div>
                  </div>
                </td>
                <td class="px-6 py-4">
                  <div class="flex items-center">
                    <span class="font-semibold" :class="getProfitClass(getItemProfit(item))">
                      {{ getItemProfitPercentage(item) }}%
                    </span>
                    <div class="ml-2 w-16 bg-gray-200 rounded-full h-1.5">
                      <div 
                        class="h-1.5 rounded-full transition-all duration-300" 
                        :class="getItemProfit(item) >= 0 ? 'bg-green-500' : 'bg-red-500'"
                        :style="{ width: Math.min(Math.abs(getItemProfitPercentage(item)), 100) + '%' }"
                      ></div>
                    </div>
                  </div>
                </td>
              </tr>
            </tbody>
            <tfoot class="bg-gray-50/50 border-t-2 border-gray-200" ref="tableFooterRef">
              <tr>
                <td class="px-6 py-4">
                  <span class="font-bold text-gray-900 text-lg">Total</span>
                </td>
                <td class="px-6 py-4">
                  <span class="font-bold text-gray-900" :class="{ 'opacity-40': activeTab === 'actuals' }">
                    €{{ activeTab !== 'actuals' ? formatNumber(currentCumulative.estCost) : '0' }}
                  </span>
                </td>
                <td class="px-6 py-4">
                  <span class="font-bold text-gray-900" :class="{ 'opacity-40': activeTab === 'estimates' }">
                    €{{ activeTab !== 'estimates' ? formatNumber(currentCumulative.actCost) : '0' }}
                  </span>
                </td>
                <td class="px-6 py-4">
                  <span class="font-bold text-gray-900" :class="{ 'opacity-40': activeTab === 'actuals' }">
                    €{{ activeTab !== 'actuals' ? formatNumber(currentCumulative.estRev) : '0' }}
                  </span>
                </td>
                <td class="px-6 py-4">
                  <span class="font-bold text-gray-900" :class="{ 'opacity-40': activeTab === 'estimates' }">
                    €{{ activeTab !== 'estimates' ? formatNumber(currentCumulative.actRev) : '0' }}
                  </span>
                </td>
                <td class="px-6 py-4">
                  <div class="flex items-center">
                    <span class="font-bold text-lg" :class="getProfitClass(getTotalProfit())">
                      €{{ formatNumber(getTotalProfit()) }}
                    </span>
                    <div class="ml-2 w-3 h-3 rounded-full" :class="getTotalProfit() >= 0 ? 'bg-green-500' : 'bg-red-500'"></div>
                  </div>
                </td>
                <td class="px-6 py-4">
                  <div class="flex items-center">
                    <span class="font-bold text-lg" :class="getProfitClass(getTotalProfit())">
                      {{ getTotalProfitPercentage() }}%
                    </span>
                    <div class="ml-2 w-20 bg-gray-200 rounded-full h-2">
                      <div 
                        class="h-2 rounded-full transition-all duration-300" 
                        :class="getTotalProfit() >= 0 ? 'bg-green-500' : 'bg-red-500'"
                        :style="{ width: Math.min(Math.abs(getTotalProfitPercentage()), 100) + '%' }"
                      ></div>
                    </div>
                  </div>
                </td>
              </tr>
            </tfoot>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, nextTick } from 'vue'
import { gsap } from 'gsap'

interface EstimateItem {
  name: string
  estCost: number
  actCost: number
  estRev: number
  actRev: number
}

interface CumulativeValues {
  estCost: number
  actCost: number
  estRev: number
  actRev: number
}

const tabs: { id: 'all' | 'estimates' | 'actuals'; label: string }[] = [
  { id: 'all', label: 'All Data' },
  { id: 'estimates', label: 'Estimates Only' },
  { id: 'actuals', label: 'Actuals Only' },
]

const activeTab = ref<'all' | 'estimates' | 'actuals'>('all')
const filterText = ref('')

// Template refs for animations
const headerRef = ref<HTMLElement>()
const statsCardsRef = ref<HTMLElement>()
const mainCardRef = ref<HTMLElement>()
const tabsRef = ref<HTMLElement>()
const searchRef = ref<HTMLElement>()
const tableRef = ref<HTMLElement>()
const tableHeaderRef = ref<HTMLElement>()
const tableBodyRef = ref<HTMLElement>()
const tableFooterRef = ref<HTMLElement>()

// Stats card refs
const estimatedProfitCard = ref<HTMLElement>()
const actualProfitCard = ref<HTMLElement>()
const totalRevenueCard = ref<HTMLElement>()
const totalCostCard = ref<HTMLElement>()
const estimatedProfitValue = ref<HTMLElement>()
const actualProfitValue = ref<HTMLElement>()
const totalRevenueValue = ref<HTMLElement>()
const totalCostValue = ref<HTMLElement>()

const items = ref<EstimateItem[]>([
  {
    name: 'Development',
    estCost: 20000,
    actCost: 22000,
    estRev: 30000,
    actRev: 28000,
  },
  {
    name: 'Design',
    estCost: 8000,
    actCost: 7500,
    estRev: 12000,
    actRev: 13000,
  },
  {
    name: 'Marketing',
    estCost: 15000,
    actCost: 18000,
    estRev: 20000,
    actRev: 17000,
  },
])

// GSAP Animations
const initAnimations = () => {
  // Set initial states
  if (headerRef.value) gsap.set(headerRef.value, { opacity: 0, y: 50 })
  if (statsCardsRef.value) gsap.set(statsCardsRef.value, { opacity: 0, y: 50 })
  if (mainCardRef.value) gsap.set(mainCardRef.value, { opacity: 0, y: 50 })
  gsap.set('.stats-card', { opacity: 0, scale: 0.8, y: 30 })
  if (tabsRef.value) gsap.set(tabsRef.value, { opacity: 0, y: 20 })
  if (searchRef.value) gsap.set(searchRef.value, { opacity: 0, y: 20 })
  if (tableHeaderRef.value) gsap.set(tableHeaderRef.value, { opacity: 0, y: 20 })
  gsap.set('.table-row', { opacity: 0, x: -50 })

  // Create timeline for initial load
  const tl = gsap.timeline({ delay: 0.2 })

  // Animate header
  if (headerRef.value) {
    tl.to(headerRef.value, {
      opacity: 1,
      y: 0,
      duration: 0.8,
      ease: 'power2.out'
    })
  }

  // Animate stats cards with stagger
  tl.to('.stats-card', {
    opacity: 1,
    scale: 1,
    y: 0,
    duration: 0.6,
    stagger: 0.1,
    ease: 'back.out(1.7)'
  }, '-=0.4')

  // Animate main card
  if (mainCardRef.value) {
    tl.to(mainCardRef.value, {
      opacity: 1,
      y: 0,
      duration: 0.8,
      ease: 'power2.out'
    }, '-=0.2')
  }

  // Animate tabs and search
  const tabElements = [tabsRef.value, searchRef.value].filter(Boolean)
  if (tabElements.length > 0) {
    tl.to(tabElements, {
      opacity: 1,
      y: 0,
      duration: 0.6,
      stagger: 0.1,
      ease: 'power2.out'
    }, '-=0.4')
  }

  // Animate table header
  if (tableHeaderRef.value) {
    tl.to(tableHeaderRef.value, {
      opacity: 1,
      y: 0,
      duration: 0.6,
      ease: 'power2.out'
    }, '-=0.3')
  }

  // Animate table rows with stagger
  tl.to('.table-row', {
    opacity: 1,
    x: 0,
    duration: 0.5,
    stagger: 0.05,
    ease: 'power2.out'
  }, '-=0.2')

  // Animate table footer
  if (tableFooterRef.value) {
    tl.to(tableFooterRef.value, {
      opacity: 1,
      y: 0,
      duration: 0.6,
      ease: 'power2.out'
    }, '-=0.3')
  }
}

const animateTabSwitch = () => {
  // Animate tab buttons
  gsap.to('.tab-button', {
    scale: 0.95,
    duration: 0.1,
    ease: 'power2.out',
    onComplete: () => {
      gsap.to('.tab-button', {
        scale: 1,
        duration: 0.1,
        ease: 'power2.out'
      })
    }
  })

  // Animate table rows with new data
  gsap.to('.table-row', {
    opacity: 0,
    x: -20,
    duration: 0.2,
    stagger: 0.02,
    ease: 'power2.in',
    onComplete: () => {
      gsap.to('.table-row', {
        opacity: 1,
        x: 0,
        duration: 0.3,
        stagger: 0.03,
        ease: 'power2.out'
      })
    }
  })
}

const animateStatsCards = () => {
  // Animate the values with a counting effect
  const valueElements = [
    estimatedProfitValue.value,
    actualProfitValue.value,
    totalRevenueValue.value,
    totalCostValue.value
  ]

  valueElements.forEach((el, index) => {
    if (el) {
      gsap.fromTo(el, 
        { scale: 1.1 },
        { 
          scale: 1,
          duration: 0.3,
          delay: index * 0.1,
          ease: 'back.out(1.7)'
        }
      )
    }
  })
}

const switchTab = (tabId: 'all' | 'estimates' | 'actuals') => {
  activeTab.value = tabId
  animateTabSwitch()
}

// Add hover animations for stats cards
const addHoverAnimations = () => {
  const cards = [estimatedProfitCard.value, actualProfitCard.value, totalRevenueCard.value, totalCostCard.value]
  
  cards.forEach(card => {
    if (card) {
      card.addEventListener('mouseenter', () => {
        gsap.to(card, {
          scale: 1.02,
          duration: 0.2,
          ease: 'power2.out'
        })
      })
      
      card.addEventListener('mouseleave', () => {
        gsap.to(card, {
          scale: 1,
          duration: 0.2,
          ease: 'power2.out'
        })
      })
    }
  })
}

// Watch for changes in computed values to animate stats
const watchStatsChanges = () => {
  // This would be implemented with watchers if needed
  // For now, we'll animate on tab changes
}

onMounted(() => {
  nextTick(() => {
    initAnimations()
    addHoverAnimations()
  })
})

const formatNumber = (num: number): string => {
  return num.toLocaleString()
}

const getItemProfit = (item: EstimateItem): number => {
  if (activeTab.value === 'estimates') return item.estRev - item.estCost
  if (activeTab.value === 'actuals') return item.actRev - item.actCost
  return item.estRev + item.actRev - (item.estCost + item.actCost)
}

const getItemCost = (item: EstimateItem): number => {
  if (activeTab.value === 'estimates') return item.estCost
  if (activeTab.value === 'actuals') return item.actCost
  return item.estCost + item.actCost
}

const getItemProfitPercentage = (item: EstimateItem): number => {
  const profit = getItemProfit(item)
  const cost = getItemCost(item)
  return cost ? Math.round((profit / cost) * 100) : 0
}

const currentCumulative = computed<CumulativeValues>(() => ({
  estCost: items.value.reduce((sum, item) => sum + item.estCost, 0),
  actCost: items.value.reduce((sum, item) => sum + item.actCost, 0),
  estRev: items.value.reduce((sum, item) => sum + item.estRev, 0),
  actRev: items.value.reduce((sum, item) => sum + item.actRev, 0),
}))

const estimatedProfit = computed(() => currentCumulative.value.estRev - currentCumulative.value.estCost)
const actualProfit = computed(() => currentCumulative.value.actRev - currentCumulative.value.actCost)

const estimatedProfitPercentage = computed(() =>
  currentCumulative.value.estCost ? Math.round((estimatedProfit.value / currentCumulative.value.estCost) * 100) : 0
)

const actualProfitPercentage = computed(() =>
  currentCumulative.value.actCost ? Math.round((actualProfit.value / currentCumulative.value.actCost) * 100) : 0
)

const getTotalProfit = (): number => {
  if (activeTab.value === 'estimates') return estimatedProfit.value
  if (activeTab.value === 'actuals') return actualProfit.value
  return estimatedProfit.value + actualProfit.value
}

const getTotalCost = (): number => {
  if (activeTab.value === 'estimates') return currentCumulative.value.estCost
  if (activeTab.value === 'actuals') return currentCumulative.value.actCost
  return currentCumulative.value.estCost + currentCumulative.value.actCost
}

const getTotalProfitPercentage = (): number => {
  const profit = getTotalProfit()
  const cost = getTotalCost()
  return cost ? Math.round((profit / cost) * 100) : 0
}

const getProfitClass = (profit: number): string => {
  return profit >= 0 ? 'text-green-600' : 'text-red-600'
}

const filteredItems = computed(() => {
  if (!filterText.value) return items.value

  const searchTerm = filterText.value.toLowerCase()
  return items.value.filter((item) =>
    Object.entries(item).some(([key, value]) =>
      key !== 'name' 
        ? value.toString().toLowerCase().includes(searchTerm) 
        : (value as string).toLowerCase().includes(searchTerm)
    )
  )
})
</script>

<style scoped>
/* Additional CSS for smooth animations */
.stats-card {
  transform-origin: center;
}

.table-row {
  transform-origin: left center;
}

/* Ensure smooth transitions for all animated elements */
* {
  will-change: transform, opacity;
}
</style>
  