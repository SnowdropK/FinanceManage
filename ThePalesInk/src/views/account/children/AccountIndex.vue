<template>
    <div class="account-wrap" id="account_bc">
        <head-title :title="'记账'"></head-title>
        <div class="block">
            <el-carousel height="220px">
            <el-carousel-item v-for="item in imgArr" :key="item.index" class="img_bz" style=" background-size: cover;">
                <img :src="item.imgsrc">
            </el-carousel-item>
            </el-carousel>
        </div>
        <div class="go-btn-box">
            <a href="#/account/consumption" class="go-account go-consumption">消费</a>
            <a href="#/account/earn" class="go-account go-earn">入账</a>
        </div>
        
    </div>
</template>
<script>
    import types from '../../../store/mutation-types'
    import headTitle from '../../../components/head-title.vue'
    export default {
        name: 'account_index',
        data(){
            return{
                imgArr:[
                    {index:1,imgsrc:'../../../../static/img/s1.jpg'},
                    {index:2,imgsrc:'../../../../static/img/s2.jpg'},
                    {index:3,imgsrc:'../../../../static/img/s3.jpg'},
                    {index:4,imgsrc:'../../../../static/img/s4.jpg'}
                ]
            }
        },
        created () {
            this.setNavIndex();
        },
        components: {
            headTitle
        },
        methods: {
            setNavIndex () {
                this.$store.commit(types.SET_NAV_INDEX,'2')
            }
        }
    }
</script>
<style lang="scss">
    @import "../../../assets/scss/define";
    #account_bc{
        background-image:url('../../../../static/img/444.jpg');
        background-size: cover;
    }
    .block{
        margin-top:-13px;
    }
    .img_bz{
        background-size: cover;
    }
    .el-carousel__item h3 {
        color: #475669;
        font-size: 14px;
        opacity: 0.75;
        line-height: 150px;
        margin: 0;
    }

    .el-carousel__item:nth-child(2n) {
        background-color: #99a9bf;
    }
    
    .el-carousel__item:nth-child(2n+1) {
        background-color: #d3dce6;
    }
    .go-btn-box{
        @extend %pa;
        @extend %tac;
        @extend %f14;
        @extend %cfff;
        @extend %l0;
        @extend %r0;
        @extend %t50;
        transform: translate3d(0,-20%,0);
    }
    .go-account{
        @extend %db;
        @extend %cp;
        @extend %cfff;
        margin: 0 auto 20px;
        width: 150px;
        height: 35px;
        line-height: 35px;
        border-radius: 5px;
        &.go-consumption{
            background-color: #FF4949;
            box-shadow: 0 3px 0 0 red;
        }
        &.go-earn{
            background-color: #69ce72;
            box-shadow: 0 3px 0 0 #13CE66;
        }
    }
</style>

