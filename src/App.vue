<template>
  <div class="container">
    <div class="row">
      <h1>Coin Market</h1>

      <input class="form-control bg-dark text-light rounded-0 border-0 my-4" v-model="textSearch" type="text" placeholder="Search coin"/>

      <table class="table table-dark">
        <thead>
          <tr>
            <th v-for="title in titles" :key="title">
              {{ title }}
            </th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="(coin, index) in filteredCoins" :key="index">
            <td class="text-muted">
              {{ index + 1 }}
            </td>
            <td>
              <img :src="coin.image" class="me-2" style="width:2rem" alt="">
              <span>{{ coin.name }}</span>
              <span class="ms-2 text-uppercase text-muted">{{ coin.symbol }}</span>
            </td>
            <td>
              ${{ coin.current_price }}
            </td>
            <td :class="[coin.price_change_percentage_24h > 0 ? 'text-success' : 'text-danger']">
              ${{ coin.price_change_percentage_24h }} %
            </td>
            <td>
              $ {{ coin.total_volume.toLocaleString() }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="mb-3 pt-0">
      
    </div>


    
  </div>
</template>

<script>
import { reactive, watch, onMounted, toRefs } from 'vue';


export default {
  setup() {
    
    const state = reactive({
      coins: [],
      filteredCoins: [],
      titles: ['#','Coin','Price','Price Change','24h Volume'],
      textSearch: ''
    });

    // mounted
    onMounted(() => {
      getCoins();
    });

    const getCoins = async() => {
      const res = await fetch('https://api.coingecko.com/api/v3/coins/markets?vs_currency=mxn&order=market_cap_desc&per_page=100&page=1&sparkline=false')
      const json = await res.json();
      state.coins = json;
      state.filteredCoins = json;
    }

    const searchCoin = () => {
      state.filteredCoins = state.coins.filter((coin) =>
        coin.name.toLowerCase().includes(state.textSearch.toLowerCase()) ||
        coin.symbol.toLowerCase().includes(state.textSearch.toLowerCase())
      );
    }

    watch(() => state.textSearch,() => {
      searchCoin();
    })

    return {
      ...toRefs(state),
      searchCoin,
    }
  }
}
</script>

<style>

</style>
