<template>
<div class="search">
    <input type="text" placeholder="Search..." list="test" v-on:keyup.13="submit" v-on:keyup="search" v-model="searchString">
    <datalist id="test">
        <option v-for="item in resultList" :key="item.name" :value="item.name"></option>
    </datalist>

</div>
</template>

<style scoped>
.search{
    display: inline-block;
}
</style>

<script>
    export default {
        data() {
            return {
                searchString: "",
                resultList: []
            }
        },
        methods: {
            search(){
                this.$http.post('http://13.59.89.132/stock-exchange-service/market/search', {searchString: this.searchString}).then(response => {
                    console.log(response.data);
                    this.resultList = response.data.stocks;
                });
            },
            submit(){
                this.$parent.searchString = this.searchString;
            }
        }
    }
</script>

