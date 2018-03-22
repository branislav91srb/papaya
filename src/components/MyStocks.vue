<template>
<div>
   <table class="table">
       <thead>
           <tr>
               <th>Stock</th>
               <th>Symbol</th>
               <th>Profitability</th>
               <th></th>
           </tr>
       </thead>
       <tbody>
            <tr v-for="item in list" :key="item.symbol" :class="{ red: item.profitability < 0, green: item.profitability > 0 }">
                <td>{{item.name}}</td>
                <td>{{item.symbol}}</td>
                <td>{{item.profitability}}</td>
                <td>
                    <button @click="sell(item.symbol)" class="btn btn-primary">Sell</button>
                </td>
            </tr>
       </tbody>
   </table>
</div>
</template>

<style scoped>
table{
    width: 100%;
}
.red{
    background: #e60c0c;
}
.green{
    background: #09d009;
}
</style>

<script>
    import bus from '../bus';

    export default {
        data() {
            return {
                list: []
            }
        },
        created(){
            this.getMyStocks();
        },
        methods: {
           getMyStocks(){
                this.$http.get('http://13.59.89.132/stock-exchange-service/portfolio').then(response => {

                    this.$parent.funds = response.data.funds;

                    let myStocks = response.data.myStocks;

                    let myStocksSymbols = myStocks.map(x => x.symbol);

                    this.$http.get('http://13.59.89.132/stock-exchange-service/market', {params: {symbol: myStocksSymbols.toString()}}).then(response => {

                        var stocks = response.data.stocks;
                        this.list = [];
                        var profitable = [];

                        for(var item of myStocks){
                            
                            var currentStock = stocks.filter(x => x.symbol === item.symbol);

                            if(currentStock.length > 0){
                                var myStock = {};
                                myStock.name = currentStock[0].name;
                                myStock.symbol = currentStock[0].symbol;
                                myStock.profitability = this.countProfitability(item.purchasePrice, currentStock[0].currentPrice, item.quantity);

                                if(myStock.profitability > 0){
                                    profitable.push(currentStock[0].symbol);
                                }

                                this.list.push(myStock);
                            }
                            
                        }

                        this.$parent.bought = this.list.map(x => x.symbol);

                        this.$parent.refreshProfitable(profitable);
                    });

                    this.$parent.searchAndFilter();
                });
           },

           sell(symbol){
               this.$http.post('http://13.59.89.132/stock-exchange-service/market/sell', {stockSymbol: symbol}).then(response => {
                    this.$parent.funds = response.data.funds;
                    this.getMyStocks();
                });
           },
           countProfitability(purchasePrice, currentPrice, quantity){
               var profitability = ((purchasePrice * quantity) - (currentPrice * quantity));
               return profitability.toFixed(2);
           }
        },
        mounted(){
            bus.$on('refreshMyStock', () => {
                this.getMyStocks();
            });
        }
    }
</script>

