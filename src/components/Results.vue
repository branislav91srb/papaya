<template>
<div>
   <table class="table">
       <thead>
           <tr>
               <th>Stock</th>
               <th>Symbol</th>
               <th>Start of commerce</th>
               <th>Current price</th>
               <th>Following</th>
               <th></th>
           </tr>
       </thead>
       <tbody>
           <tr v-for="item in list" :key="item.symbol" v-show="showRow(item)">
            <td>{{item.name}}</td>
            <td>{{item.symbol}}</td>
            <td>{{item.startOfCommerce}}</td>
            <td>{{item.currentPrice}}</td>
            <td><input type="checkbox" :checked="item.following" disabled/></td>
            <td>
                <button class="btn btn-primary" @click="buy(item.symbol)" :disabled="$parent.bought.includes(item.symbol) || $parent.funds < item.currentPrice">Buy</button>
                <button class="btn btn-danger" @click="sell(item.symbol)" :disabled="!$parent.bought.includes(item.symbol)">Sell</button>
            </td>
            </tr>
       </tbody>
   </table>
</div>
</template>

<style scoped>
table {
  width: 100%;
}
</style>

<script>
import bus from '../bus';

export default {
  props: ["list", "canBuy"],
  data() {
    return {
      following: []
    };
  },
  methods: {
    showRow(item) {
      var showCanbuy = !this.$parent.canBuy || (this.$parent.canBuy && this.$parent.funds >= item.currentPrice);
      var showFollowing = !this.$parent.following || (this.$parent.following && item.following);

      var show = showCanbuy && showFollowing;
      return show;
    },
    buy(symbol) {
      this.$http
        .post("http://13.59.89.132/stock-exchange-service/market/buy", {
          stockSymbol: symbol, 
          stockQuantity: 1
        })
        .then(response => {
            var funds = response.data.funds;
            this.$parent.funds = funds;
            this.$parent.refreshMyStock();
        });
    },
    sell(symbol) {
      this.$http
        .post("http://13.59.89.132/stock-exchange-service/market/sell", {
          stockSymbol: symbol
        })
        .then(response => {
            var funds = response.data.funds;
            this.$parent.funds = funds;
            this.$parent.refreshMyStock();
        });
    }
  },
  mounted(){
      bus.$on('refreshProfitable', profitable => {
          this.following = profitable;
      });
  }
};
</script>

