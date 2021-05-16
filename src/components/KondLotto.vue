<template>
  <template v-if="btcToUsd > 0">
    <div class="container mx-auto p-8">
      <div class="bg-white rounded-xl shadow-md overflow-hidden md:max-w-xl mx-auto">
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
<!--    <p>USD {{ usdToHuf }} as of {{ usdAsOf }}</p>-->
<!--    <p>BTC {{ btcToUsd }} as of {{ lastBitcoinFetch }}</p>-->
  </template>
  <template v-else>
    <p class="text-center italic text-gray-500">loading...</p>
  </template>
</template>

<script lang="ts">
const LOTTERY_TICKET_PRICE_HUF = 300;
const BTC_AMOUNT = 0.00202943;
const BTC_BOUGHT_FOR_HUF = 30000;

const ROUNDING = 1000;
const BTC_FETCH_INTERVAL = 5000;

import axios from "axios";

export default {
  name: "KondLotto",
  data() {
    return {
      btcToUsd: null as number | null,
      usdToHuf: null as number | null,
      lastBitcoinFetch: null as string | null,
      lastUsdFetch: null as string | null,
      usdAsOf: null as string | null,
    }
  },
  computed: {
    ticketCount(): number | null {
      if (this.btcToUsd === null || this.usdToHuf === null) {
        return null;
      }
      const btcPriceHuf = this.btcToUsd * this.usdToHuf;
      const btcWealthHuf = BTC_AMOUNT * btcPriceHuf;
      const profitHuf = btcWealthHuf - BTC_BOUGHT_FOR_HUF;
      const count = profitHuf / LOTTERY_TICKET_PRICE_HUF;
      const rounded = Math.round(count * ROUNDING) / ROUNDING;
      return rounded;
    }
  },
  beforeMount() {
    this.getBitcoinPrice();
    this.getUsdHufPrice();
  },
  methods: {
    async getBitcoinPrice() {
      const res = await fetch('https://api.coindesk.com/v1/bpi/currentprice.json');
      const data = await res.json();
      this.btcToUsd = data?.bpi?.USD?.rate_float;
      this.lastBitcoinFetch = new Date().toISOString();
      setTimeout(() => this.getBitcoinPrice(), BTC_FETCH_INTERVAL);
    },
    async getUsdHufPrice() {
      const options = {
        method: 'GET',
        url: 'https://alpha-vantage.p.rapidapi.com/query',
        params: { to_currency: 'HUF', function: 'CURRENCY_EXCHANGE_RATE', from_currency: 'USD' },
        headers: {
          'x-rapidapi-key': 'a50a0d0c08mshe623dee645b3d07p19c46ajsn7e085d5b03a3',
          'x-rapidapi-host': 'alpha-vantage.p.rapidapi.com'
        }
      };

      const response = await axios.request(options);
      console.log(JSON.stringify(response.data));
      const exchange = response.data["Realtime Currency Exchange Rate"];
      this.usdToHuf = exchange["5. Exchange Rate"];
      this.usdAsOf = exchange["6. Last Refreshed"];
    }
  }
}
</script>
