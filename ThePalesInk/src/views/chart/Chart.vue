<template>
    <div class="container-view">
        <head-title :title="'分析'"></head-title>
        <div class="chart-wrap">
            <scroller lock-x
                      height="-118"
                      :scrollbarY="true"
                      @on-scroll="onScroll"
                      ref="chartScrollEvent"
                      class="ppp">
                <div class="chart-con">
                    <!-- 图表模块 -->
                    <el-tabs type="border-card">
                        <el-tab-pane label="消费图表" style="margin-left:20px;">
                            <div class="chart-item">
                                <h2 class="chart-title">消费状况：</h2>
                                <canvas id="consumption-chart" width="300" height="300"></canvas>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="收入图表">
                            <div class="chart-item">
                                <h2 class="chart-title">收入状况：</h2>
                                <canvas id="earn-chart" width="300" height="300"></canvas>
                            </div>
                        </el-tab-pane>
                    </el-tabs>
                </div>
            </scroller>
        </div>
    </div>
</template>
<script>
    import types from '../../store/mutation-types'
    import headTitle from '../../components/head-title.vue'
    import Util from '../../assets/lib/Util'
    import Tool from '../../assets/lib/Tool'
    import { Scroller } from 'vux'
    export default {
        name: 'chart',
        data () {
            return {
                consumption_chart_arr: [0,0,0,0,0,0,0,0],
                earn_chart_arr: [0,0,0]
            }
        },
        components: {
            Scroller,
            headTitle
        },
        created () {
            this.$store.commit(types.SET_NAV_INDEX,'4');
            this.fetchBillData();
        },
        methods: {
            onScroll (pos) {
                this.scrollTop = pos.top;
            },
            fetchBillData (query_condition) {
                var bill_arr = [];
                this.$vux.loading.show({text:'Loading'});
                var user_name = Tool.dataToSessionStorageOperate.achieve('user').user_name;
                var obj = {};
                obj.user_name = user_name;
                query_condition && (obj.query_condition = query_condition);
                Util.fetchBill(obj,(result) => {
                    setTimeout(() => {
                        this.$vux.loading.hide();
                        if (result.status == 1) {
                            bill_arr = result.data.bills;
                            bill_arr.forEach((item,index) =>{
                                if ( item.bill_account_type[0] == '水果零食' )
                                    this.consumption_chart_arr[0] = this.consumption_chart_arr[0] + (+item.bill_sum);
                                else if ( item.bill_account_type[0] == '餐饮伙食' )
                                    this.consumption_chart_arr[1] = this.consumption_chart_arr[1] + (+item.bill_sum);
                                else if ( item.bill_account_type[0] == '出行旅游' )
                                    this.consumption_chart_arr[2] = this.consumption_chart_arr[2] + (+item.bill_sum);
                                else if ( item.bill_account_type[0] == '网上购物' )
                                    this.consumption_chart_arr[3] = this.consumption_chart_arr[3] + (+item.bill_sum);
                                else if ( item.bill_account_type[0] == '生活日常' )
                                    this.consumption_chart_arr[4] = this.consumption_chart_arr[4] + (+item.bill_sum);
                                else if ( item.bill_account_type[0] == '租房水电' )
                                    this.consumption_chart_arr[5] = this.consumption_chart_arr[5] + (+item.bill_sum);
                                else if ( item.bill_account_type[0] == '医疗药物' )
                                    this.consumption_chart_arr[6] = this.consumption_chart_arr[6] + (+item.bill_sum);
                                else if ( item.bill_account_type[0] == '其它消费' )
                                    this.consumption_chart_arr[7] = this.consumption_chart_arr[7] + (+item.bill_sum);
                                else if ( item.bill_account_type[0] == '基本工资' )
                                    this.earn_chart_arr[0] = this.earn_chart_arr[0] + (+item.bill_sum);
                                else if ( item.bill_account_type[0] == '公司福利' )
                                    this.earn_chart_arr[1] = this.earn_chart_arr[1] + (+item.bill_sum);
                                else if ( item.bill_account_type[0] == '其它入账' )
                                    this.earn_chart_arr[2] = this.earn_chart_arr[2] + (+item.bill_sum);
                            });
                            this.$nextTick(() => {
                                var consumption_data = {
                                    labels: [
                                        "水果零食",
                                        "餐饮伙食",
                                        "出行旅游",
                                        "网上购物",
                                        "生活日常",
                                        "租房水电",
                                        "医疗药物",
                                        "其它消费",
                                    ],
                                    datasets: [{
                                        data: this.consumption_chart_arr,
                                        backgroundColor: [
                                            "#999933",
                                            "#FF9933",
                                            "#FF6666",
                                            "#36A2EB",
                                            "#666699",
                                            "#CCFF00",
                                            "#66CCCC",
                                            "#663366"
                                        ]
                                    }]
                                };
                                var earn_data = {
                                    labels: [
                                        "基本工资",
                                        "公司福利",
                                        "其它入账"
                                    ],
                                    datasets: [{
                                        data: this.earn_chart_arr,
                                        backgroundColor: [
                                            "#13CE66",
                                            "#F7BA2A",
                                            "#FF4949"
                                        ]
                                    }]
                                };
                                var consumption_ctx = document.getElementById("consumption-chart").getContext("2d");
                                var earn_ctx = document.getElementById("earn-chart").getContext("2d");
                                new Chart(consumption_ctx,{
                                    type: 'pie',
                                    data: consumption_data
                                });
                                new Chart(earn_ctx,{
                                    type: 'pie',
                                    data: earn_data
                                });
                                this.$refs.chartScrollEvent.reset();
                            })
                        }
                    },100)
                });
            }
        }
    }
</script>
<style lang="scss">
    @import "../../assets/scss/define";
    .ppp{
        background-image:url('../../../static/img/princess.jpg');
        background-size: cover;
        height: 641px!important;
    }
    .el-tabs__nav-wrap{
        margin-top:-1px;
    }
    .el-tabs--border-card>.el-tabs__header {
        background-color: #c4afe9 !important;
        border-bottom: 1px solid #e4e7ed;
    }
    .el-tabs--border-card>.el-tabs__header .el-tabs__item{
        color:#fff !important;
    }
    .el-tabs--border-card>.el-tabs__header .el-tabs__item.is-active{
        color:#7e57c2 !important;
    }
    .el-tabs--border-card>.el-tabs__content{
        height:658px;
    }
    .el-tabs--border-card>.el-tabs__content{
       background-color:rgba(0,0,0,0);
       background-image:url('../../../static/img/princess.jpg');
       background-size: cover;
       height: 641px;
    }
    .el-tabs--border-card{
        
    }
    .el-tabs__nav{
        margin-left:110px;
    }
    .chart-wrap{
        @extend %oya;
        @extend %pa;
        @extend %w100;
        @extend %ios;
        @extend %b0;
        top: 43px;
    }
    .chart-title{
        @extend %f14;
        @extend %fwn;
        color: #58B7FF;
    }
    .chart-item{
       
    }
</style>
