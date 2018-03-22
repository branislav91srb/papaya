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
           <tr v-for="item in list" :key="item.symbol">
            <td>{{item.name}}</td>
            <td>{{item.symbol}}</td>
            <td>{{item.startOfCommerce}}</td>
            <td>{{item.currentPrice}}</td>
            <td><input type="checkbox" :checked="item.following" disabled/></td>
            <td>
                <button class="btn btn-primary">Buy</button>
                <button class="btn btn-primary">Sell</button>
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
</style>

<script>
    export default {
        props: [
            'list'
        ],
        data() {
            return {
            }
        },
        created(){
            this.getAll();
        },
        methods: {
            getAll(){
                this.$http.post('http://13.59.89.132/stock-exchange-service/market/search', {searchString: ""}).then(response => {
                    console.log(response.data);
                    this.$parent.search(response.data.stocks);
                });
            }
        }
    }
</script>

