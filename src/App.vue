<template>
  <div class="min-h-screen bg-neutral-950 text-white flex flex-col items-center justify-center p-6 relative overflow-hidden font-sans">
    <!-- Background glowing orbs for premium effect -->
    <div class="absolute top-[-10%] left-[-10%] w-[40vw] h-[40vw] bg-emerald-500/20 rounded-full blur-[100px] pointer-events-none"></div>
    <div class="absolute bottom-[-10%] right-[-10%] w-[40vw] h-[40vw] bg-indigo-500/20 rounded-full blur-[100px] pointer-events-none"></div>

    <div class="z-10 w-full max-w-3xl flex flex-col items-center">
      <header class="text-center mb-12">
        <h1 class="text-4xl md:text-5xl font-extrabold tracking-tight bg-gradient-to-r from-emerald-400 to-cyan-400 bg-clip-text text-transparent mb-4">
          Crypto Pulse
        </h1>
        <p class="text-neutral-400 text-lg">Real-time Bitcoin & Ethereum Tracker</p>
      </header>

      <!-- Loading / Error State -->
      <div v-if="error" class="bg-red-500/10 border border-red-500/20 text-red-400 px-6 py-4 rounded-xl backdrop-blur-md mb-8">
        {{ error }}
      </div>

      <div class="grid grid-cols-1 md:grid-cols-2 gap-8 w-full">
        <!-- BTC Card -->
        <CryptoCard
          name="Bitcoin1"
          symbol="BTC"
          :price="prices.BTC.current"
          :previousPrice="prices.BTC.previous"
          :isLoading="isLoading"
          iconUrl="https://assets.coingecko.com/coins/images/1/large/bitcoin.png"
        />

        <!-- ETH Card -->
        <CryptoCard
          name="Ethereum1"
          symbol="ETH"
          :price="prices.ETH.current"
          :previousPrice="prices.ETH.previous"
          :isLoading="isLoading"
          iconUrl="https://assets.coingecko.com/coins/images/279/large/ethereum.png"
        />
      </div>

      <footer class="mt-16 text-neutral-500 text-sm flex items-center justify-center space-x-2">
        <div class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></div>
        <span>Live Updates via Binance API (5s)</span>
      </footer>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import CryptoCard from './components/CryptoCard.vue'

const prices = ref({
  BTC: { current: null, previous: null },
  ETH: { current: null, previous: null }
})
const isLoading = ref(true)
const error = ref(null)

let intervalId = null

const fetchPrices = async () => {
  try {
    const symbols = ['BTCUSDT', 'ETHUSDT']
    const responses = await Promise.all(
      symbols.map(symbol => fetch(`https://api.binance.com/api/v3/ticker/price?symbol=${symbol}`))
    )
    
    for (const res of responses) {
      if (!res.ok) throw new Error('Failed to fetch data')
    }

    const [btcData, ethData] = await Promise.all(responses.map(res => res.json()))

    updatePrice('BTC', parseFloat(btcData.price))
    updatePrice('ETH', parseFloat(ethData.price))
    
    error.value = null
  } catch (err) {
    console.error(err)
    error.value = 'Failed to fetch the latest prices. Retrying...'
  } finally {
    isLoading.value = false
  }
}

const updatePrice = (coin, newPrice) => {
  const current = prices.value[coin].current
  prices.value[coin] = {
    previous: current,
    current: newPrice
  }
}

onMounted(() => {
  fetchPrices()
  intervalId = setInterval(fetchPrices, 5000)
})

onUnmounted(() => {
  if (intervalId) clearInterval(intervalId)
})
</script>

<style>
/* Add any global subtle animations if needed */
</style>
