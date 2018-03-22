<template>
<div class="filter">
    <label for="canBuy">
        <input id="canBuy" type="checkbox" v-model="canBuy" @change="filter()"/>
        Can Buy
    </label>

    <label for="following">
        <input id="following" type="checkbox" v-model="following" @change="filter()"/>
        Following
    </label>

</div>
</template>

<style scoped>
label{
    padding-right: 10px;
}
.filter{
    float: right;
}
</style>

<script>
    export default {
        data() {
            return {
                canBuy: false,
                following: false
            }
        },
        methods: {
            filter(){
                this.$parent.canBuy = this.canBuy;
                this.$parent.following = this.following;
            }
        }
    }
</script>

