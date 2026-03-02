<template>
  <div class="group relative rounded-2xl p-[1px] bg-gradient-to-b from-white/10 to-transparent hover:from-white/20 transition-all duration-500 overflow-hidden">
    <!-- Inner Card Container -->
    <div class="relative bg-neutral-900/80 backdrop-blur-2xl rounded-2xl p-6 h-full flex flex-col justify-between shadow-2xl">
      
      <!-- Header -->
      <div class="flex items-center justify-between mb-8">
        <div class="flex items-center space-x-4">
          <div class="w-12 h-12 rounded-full overflow-hidden bg-white/5 flex items-center justify-center border border-white/10">
            <img v-if="iconUrl" :src="iconUrl" :alt="name" class="w-8 h-8 object-contain" />
          </div>
          <div>
            <h2 class="text-xl font-bold text-white tracking-wide">{{ name }}</h2>
            <p class="text-xs text-neutral-400 uppercase tracking-widest font-semibold">{{ symbol }}</p>
          </div>
        </div>
        
        <!-- Live Indicator -->
        <div class="flex items-center space-x-2">
          <span class="relative flex h-2 w-2">
            <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span>
            <span class="relative inline-flex rounded-full h-2 w-2 bg-emerald-500"></span>
          </span>
          <span class="text-[10px] text-neutral-500 uppercase tracking-wider font-bold">Live</span>
        </div>
      </div>

      <!-- Price Section -->
      <div>
        <p class="text-sm text-neutral-400 mb-1">Current Price</p>
        
        <div v-if="isLoading" class="h-10 w-32 bg-white/5 animate-pulse rounded-lg"></div>
        
        <!-- Interactive Price Display -->
        <div 
          v-else 
          class="flex items-baseline space-x-2 transition-colors duration-500"
        >
          <span class="text-3xl font-light text-neutral-500">$</span>
          <span 
            class="text-4xl md:text-5xl font-bold"
            :class="priceColorClass"
          >
            {{ formattedPrice }}
          </span>
          
          <!-- Indicator Arrow -->
          <span v-if="status === 'up'" class="text-emerald-500 animate-bounce">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 10l7-7m0 0l7 7m-7-7v18"></path></svg>
          </span>
          <span v-else-if="status === 'down'" class="text-red-500 animate-bounce">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 14l-7 7m0 0l-7-7m7 7V3"></path></svg>
          </span>
        </div>
      </div>
      
      <!-- Gradient glow on hover -->
      <div 
        class="absolute inset-0 bg-gradient-to-tr opacity-0 group-hover:opacity-10 transition-opacity duration-700 pointer-events-none"
        :class="status === 'up' ? 'from-emerald-500 to-transparent' : status === 'down' ? 'from-red-500 to-transparent' : 'from-indigo-500 to-transparent'"
      ></div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  name: String,
  symbol: String,
  price: Number,
  previousPrice: Number,
  isLoading: Boolean,
  iconUrl: String
})

const formattedPrice = computed(() => {
  if (props.price === null || isNaN(props.price)) return '0.00'
  return new Intl.NumberFormat('en-US', {
    minimumFractionDigits: 2,
    maximumFractionDigits: 2
  }).format(props.price)
})

const status = computed(() => {
  if (props.previousPrice === null || props.price === null) return 'neutral'
  if (props.price > props.previousPrice) return 'up'
  if (props.price < props.previousPrice) return 'down'
  return 'neutral'
})

const priceColorClass = computed(() => {
  switch(status.value) {
    case 'up': return 'text-emerald-400 drop-shadow-[0_0_8px_rgba(16,185,129,0.3)]'
    case 'down': return 'text-red-400 drop-shadow-[0_0_8px_rgba(239,68,68,0.3)]'
    default: return 'text-white'
  }
})
</script>
