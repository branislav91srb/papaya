<template>
  <div id="app">
    <h3>Current funds: {{funds}}</h3>
    <search></search>
    <results :list="results"></results>

    <h3>My Stocks</h3>
    <my-stocks></my-stocks>

  </div>
</template>

<style scoped>
#app{
  width:50%;
  margin: 0 auto;
}
</style>

<script>

import Vue from 'vue';
import Search from './components/Search.vue';
import Results from './components/Results.vue';
import MyStocks from './components/MyStocks.vue';

export default {
  data() {
    return {
      results: [],
      funds: 0,
      profitable: []
    }
  },
  components: {
      Search,
      Results,
      MyStocks
  },
  methods: {
    search(res){
      console.log("profitable", this.profitable);
      console.log("res", res);

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

      console.log("results", this.results);
    }
    
  }
}
</script>

