<template>
  <template v-if="bitcoinPrice > 0">
    <div class="max-w-md mx-auto mt-8">
      <div class="bg-white rounded-xl shadow-md overflow-hidden md:max-w-2xl mx-8">
        <div class="p-8 text-lg font-sans text-center">
          <p>Kond már</p>
          <p class="text-6xl p-3 font-bold">
          <span :class="ticketCount < 0 ? 'text-red-400' : 'text-green-500' ">
          {{ ticketCount }}
          </span>
          </p>
          <p>lottószelvényt nyert Bitcoinnal.</p>
        </div>
      </div>
    </div>
    <div class="absolute bottom-2 container">
      <p class="text-center text-xs text-gray-400">Last data fetch: {{ lastFetch }} by CoinDesk</p>
    </div>
  </template>
  <template v-else>
    <p class="text-center italic text-gray-500">loading...</p>
  </template>
</template>

<script lang="ts">
const TICKET_PRICE = 1;
const BTC_BUY_PRICE = 49309;
const BTC_BUY_USD = 111;
const BTC_COUNT = BTC_BUY_USD / BTC_BUY_PRICE;
const ROUNDING = 1000;
const FETCH_INTERVAL = 5000;

export default {
  name: "KondLotto",
  data() {
    return {
      bitcoinPrice: -1,
      lastFetch: null,
    }
  },
  computed: {
    ticketCount(): number | null {
      if (this.bitcoinPrice <= 0) return null;
      const profit = (this.bitcoinPrice - BTC_BUY_PRICE) * BTC_COUNT;
      const count = profit / TICKET_PRICE;
      const rounded = Math.round(count * ROUNDING) / ROUNDING;
      return rounded;
    }
  },
  beforeMount() {
    this.getBitcoinPrice();
  },
  methods: {
    async getBitcoinPrice() {
      const res = await fetch('https://api.coindesk.com/v1/bpi/currentprice.json');
      const data = await res.json();
      this.bitcoinPrice = data?.bpi?.USD?.rate_float;
      this.lastFetch = new Date().toISOString();
      setTimeout(() => this.getBitcoinPrice(), FETCH_INTERVAL);
    }
  }
}
</script>
