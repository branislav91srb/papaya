<template>
  <div id="app">
    <h3 class="funds">Current funds: {{funds}}</h3>
    <hr>

    <div class="filters">
      <search></search>
      <stock-filter></stock-filter>
    </div>

    <results :list="results" :canBuy="canBuy" :following="following"></results>

    <h3>My Stocks</h3>
    <my-stocks></my-stocks>

  </div>
</template>

<style scoped>
#app{
  width:50%;
  margin: 0 auto;
}
.funds{
  text-align: right;
}
</style>

<script>

import Vue from 'vue';
import Search from './components/Search.vue';
import StockFilter from './components/StockFilter.vue';
import Results from './components/Results.vue';
import MyStocks from './components/MyStocks.vue';

import bus from './bus';

export default {
  data() {
    return {
      results: [],
      funds: 0,
      profitable: [],
      bought: [],

      search: "",
      canBuy: false,
      following: false
    }
  },
  components: {
      Search,
      Results,
      MyStocks,
      StockFilter
  },
  methods: {
    searchAndFilter(){
      this.$http.post('http://13.59.89.132/stock-exchange-service/market/search', {searchString: this.search}).then(response => {
          
          var res = response.data.stocks;

          this.results = [];
      
          for(var item of res){
              var result = {};

              result.name = item.name;
              result.symbol = item.symbol;
              result.startOfCommerce = item.startOfCommerce;
              result.currentPrice = item.currentPrice;
              result.following = this.profitable.includes(item.symbol);

              this.results.push(result);
          }

          if(this.canBuy){
            this.results.filter(x => x.currentPrice <= this.funds);
          }

          if(this.following){
            this.results.filter(x => x.following);
          }

      });
    },
    refreshMyStock(){
      bus.$emit('refreshMyStock');
    },
    refreshProfitable(profitable){
      this.profitable = profitable;
      bus.$emit('refreshProfitable', this.profitable);
    }
  }
}
</script>

