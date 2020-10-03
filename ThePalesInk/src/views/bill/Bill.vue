<template>
    <div class="container-view">
        <!--主体内容-->
        <div class="container-box"
            :class="{'open-menu': is_open_menu}">
            <head-title :title="'账单'"></head-title>
            <svg @click="is_open_menu = !is_open_menu" class="bill-filter">
                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#filter-icon"></use>
            </svg>
            <!--过滤信息菜单-->
            <div class="filter-menu">
                <head-title :title="'筛选'"></head-title>
                <ul class="input-warp" >
                    <li class="input-item">
                        <datetime
                            title="年："
                            v-model="year_value"
                            format="YYYY"
                            confirm-text="完成"
                            cancel-text="取消">
                        </datetime>
                    </li>
                    <li class="input-item" v-show="year_value">
                        <datetime
                            title="月："
                            v-model="month_value"
                            format="MM"
                            confirm-text="完成"
                            cancel-text="取消">
                        </datetime>
                    </li>
                    <li class="input-item" v-show="month_value">
                        <datetime
                            title="日："
                            v-model="day_value"
                            format="DD"
                            confirm-text="完成"
                            cancel-text="取消">
                        </datetime>
                    </li>
                </ul>
                <div class="menu-type-wrap" >
                    <checker
                        v-model="check_value_arr"
                        type="checkbox"
                        default-item-class="bill-type-check-item"
                        selected-item-class="bill-type-check-item-selected">
                        <checker-item :value="'sgls'">
                            <svg class="checker-item-icon">
                                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-sgls"></use>
                            </svg>
                        </checker-item>
                        <checker-item :value="'cyhs'">
                            <svg class="checker-item-icon">
                                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-cyhs"></use>
                            </svg>
                        </checker-item>
                        <checker-item :value="'cxly'">
                            <svg class="checker-item-icon">
                                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-cxly"></use>
                            </svg>
                        </checker-item>
                        <checker-item :value="'wsgw'">
                            <svg class="checker-item-icon">
                                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-wsgw"></use>
                            </svg>
                        </checker-item>
                        <checker-item :value="'shrc'">
                            <svg class="checker-item-icon">
                                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-shrc"></use>
                            </svg>
                        </checker-item>
                        <checker-item :value="'cfsd'">
                            <svg class="checker-item-icon">
                                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-cfsd"></use>
                            </svg>
                        </checker-item>
                        <checker-item :value="'ylyw'">
                            <svg class="checker-item-icon">
                                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-ylyw"></use>
                            </svg>
                        </checker-item>
                        <checker-item :value="'jbgz'">
                            <svg class="checker-item-icon">
                                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-jbgz"></use>
                            </svg>
                        </checker-item>
                        <checker-item :value="'gsfl'">
                            <svg class="checker-item-icon">
                                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-gsfl"></use>
                            </svg>
                        </checker-item>
                        <checker-item :value="'qt'">
                            <svg class="checker-item-icon">
                                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-qt"></use>
                            </svg>
                        </checker-item>
                    </checker>
                </div>
                <div class="menu-btn-wrap">
                    <i class="menu-btn menu-sure-btn" @click="filterBill()">确定</i>
                    <i class="menu-btn menu-reset-btn" @click="resetFilter()">重置</i>
                </div>
            </div>
            <!--/过滤信息菜单-->
            <!--账单信息-->
            <div class="bill-wrap">
                <div class="bill-null-warp" v-show="!bill_arr.length" style="color:white;">
                    <svg class="bill-list-null-icon">
                        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#null-icon"></use>
                    </svg>
                    <span style="font-size:18px;margin-top:-10px;">没有相关账单</span>
                </div>
                <!--
                          :use-pulldown="true"
                          :use-pullup="true"
                          @on-pulldown-loading="onPullDownLoading"
                          @on-pullup-loading="onPullUpLoading"
                          :pulldown-config="pull_down_config"
                          :pullup-config="pull_up_config"
                -->
                <scroller v-show="bill_arr.length"
                          lock-x
                          height="-118"
                          :scrollbarY="true"
                          @on-scroll="onScroll"
                          ref="billScrollEvent"
                          class="bbb">
                    <ul class="bill-list">
                        <li class="bill-item" v-for="(bill_item,bill_index) in bill_arr" :key="bill_index">
                            <span class="bill-item-type"
                                :class="{'earn-type': bill_item.bill_consumption_or_earn == 1,
                                 'consumption-type': bill_item.bill_consumption_or_earn == 0}">
                                <svg class="bill-item-type-icon">
                                    <use xmlns:xlink="http://www.w3.org/1999/xlink" :xlink:href="'#type-'+ bill_item.bill_type_number"></use>
                                </svg>
                                <span class="bill-item-remark" v-text="bill_item.bill_remarks || bill_item.bill_account_type[0]"></span>
                            </span>
                            <p class="bill-item-con">                             
                                <span class="bill-item-sum" v-text="bill_item.bill_sum"></span>
                            </p>
                            <p class="bill-item-time">{{bill_item.bill_consumption_or_earn == 1 ? '入账' : '消费'}}时间</p>
                            <p class="bill-item-time">{{bill_item.bill_date}} {{bill_item.bill_time}}</p>
                            <i class="bill-cancel" @click="confirmRemoveBill(bill_item)">X</i>
                        </li>
                        <li class="bill-item-null"></li>
                    </ul>
                </scroller>
            </div>
            <!--/账单信息-->
            <!--账单信息提示-->
            <div class="bill-prompt-wrap">
                <div class="bill-prompt1">
                    <span class="bill-sum-title2">余额￥</span>
                    <span class="bill-sum2 bill-sum-balance" id="balance-sum"></span>
                </div>
                <div class="bill-prompt2" >
                    <span class="bill-sum-title" style="float:left;">入账：</span>
                    <span class="bill-sum bill-sum-earn" id="earn-sum" style="float:left;"></span>
                </div>
                <!-- <i class="bill-reduce"></i> -->
                <div class="bill-prompt3" >
                    <span class="bill-sum-title">消费：</span>
                    <span class="bill-sum bill-sum-consumption" id="consumption-sum"></span>
                </div>
                <!-- <i class="bill-equal"></i> -->
                
            </div>
            <!--/账单信息提示-->
        </div>
        <!--/主体内容-->
        <!--弹窗提示-->
        <x-dialog v-model="show_dialog" class="dialog-demo" hide-on-blur>
            <div class="dialog-con">
                确定移除该条记录？
            </div>
            <span class="bill-dialog-close"  @click="show_dialog = false"></span>
            <div class="bill-dialog-box" @click="removeBill()">
                <span class="bill-dialog-ok"></span>
            </div>
        </x-dialog>
        <!--/弹窗提示-->
    </div>
</template>
<script>
    import { Scroller, Datetime , Checker, CheckerItem ,XDialog } from 'vux'
    import headTitle from '../../components/head-title.vue'
    import GestureMobile from '../../assets/lib/GestureMobile'
    import Util from '../../assets/lib/Util'
    import CountUp from '../../assets/lib/countUp'
    import Tool from '../../assets/lib/Tool'
    import types from '../../store/mutation-types'
    export default {
        name: 'bill',
        data () {
            return {
                remove_bill: '',
                data_list: [],
                formatDemoValue: ['01', '12'],
                format: function (value, name) {
                    return `${value[0]}:${value[1]}`
                },
                earn_sum: 0,
                consumption_sum: 0,
                bill_arr: [],
                show_dialog: false,
                check_value_arr: '',
                is_btn_active: false,
                day_value: '',
                month_value: '',
                year_value: '',
                is_open_menu: false,
                pull_down_config: {
                    content: '下拉刷新',
                    height: 60,
                    autoRefresh: false,
                    downContent: '拉人家干嘛~~~',
                    upContent: '疼~还不松开人家',
                    loadingContent: 'Loading...',
                    clsPrefix: 'xs-plugin-pulldown-'
                },
                pull_up_config: {
                    content: '上拉加载',
                    pullUpHeight: 60,
                    height: 40,
                    autoRefresh: false,
                    downContent: '疼~还不松开人家',
                    upContent: '拉人家干嘛~~~',
                    loadingContent: 'Loading...',
                    clsPrefix: 'xs-plugin-pullup-'
                },
                is_timer: false
            }
        },
        created () {
            /**设置导航条状态*/
            this.$store.commit(types.SET_NAV_INDEX,'3');
            /**手势判断*/
            this.gestureMobile();
            /**获取账单列表*/
            this.fetchBillArr();
            
        },
        components: {
            Scroller,
            Datetime,
            Checker,
            CheckerItem,
            XDialog,
            headTitle
        },
        methods: {
            /**过滤账单*/
            filterBill () {
                var query_condition = {
                    year_value: this.year_value,
                    month_value: this.month_value,
                    day_value: this.day_value,
                    check_value_arr: this.check_value_arr
                };
                this.fetchBillArr(query_condition);
                this.countSum();
                this.is_open_menu = false;
            },
            /**重置删选条件*/
            resetFilter () {
                this.day_value = '';
                this.month_value = '';
                this.year_value = '';
                this.check_value_arr = '';
            },
            /**弹窗对话*/
            confirmRemoveBill (bill) {
                this.show_dialog = true;
                this.remove_bill = bill;
            },
            /**删除账单信息*/
            removeBill () {
                if(this.is_timer) return;
                this.is_timer = true;
                this.$vux.loading.show({text:'Loading'});
                var user_name = Tool.dataToSessionStorageOperate.achieve('user').user_name;
                Util.removeBill(user_name,this.remove_bill,(result) => {
                    setTimeout(() => {
                        this.is_timer = false;
                        this.$vux.loading.hide();
                        if(result.status == 1) {
                            this.fetchBillArr();
                        }
                        this.showMsg(result.msg);
                        this.show_dialog = false;
                    },100)
                })

            },
            /**获取账单信息*/
            fetchBillArr (query_condition) {
                this.$vux.loading.show({text:'Loading'});
                var user_name = Tool.dataToSessionStorageOperate.achieve('user').user_name;
                var obj = {};
                obj.user_name = user_name;
                query_condition && (obj.query_condition = query_condition);
                Util.fetchBill(obj,(result) => {
                    setTimeout(() => {
                        this.$vux.loading.hide();
                        if (result.status == 1) {
                            this.bill_arr = result.data.bills;
                            localStorage.bill_maid=JSON.stringify(this.bill_arr);
                            // this.bill_arr.reverse();
                            //时间排序bill_item.bill_time
                            var compare = function (obj1, obj2) {
                                var val1 = obj1.bill_date+obj1.bill_time;
                                var val2 = obj2.bill_date+obj2.bill_time;
                                if (val1 < val2) {
                                    return 1;
                                } else if (val1 > val2) {
                                    return -1;
                                } else {
                                    return 1;
                                }            
                            } 
                            this.bill_arr.sort(compare);
                        }
                        this.$nextTick(() => {
                            this.$refs.billScrollEvent.reset();
                        });
                        /**提示信息*/
                        this.countSum();
                    },100)
                });

            },
            /**提示信息*/
            showMsg (msg) {
                this.$vux.toast.show({
                    type: 'text',
                    width: 'auto',
                    text: msg,
                    position: 'top'
                })
            },
            /**手势判断*/
            gestureMobile () {
                this.$nextTick(() => {
                    let _this = this;
                    GestureMobile(this.$el,{
                        leftCallBackFun () {
                            _this.is_open_menu = true;
                        },
                        rightCallBackFun () {
                            _this.is_open_menu = false;
                        }
                    });
                })
            },
            /**计算金额*/
            countSum () {
                var earn_sum = this.earn_sum,
                    consumption_sum = this.consumption_sum;
                this.earn_sum = 0;
                this.consumption_sum = 0;
                this.bill_arr.forEach((item,index) => {
                    if(item.bill_consumption_or_earn == 1)
                        this.earn_sum =  this.earn_sum + (+item.bill_sum);
                    else
                        this.consumption_sum =  this.consumption_sum + (+item.bill_sum);
                });
                this.$nextTick(() => {
                    new CountUp("earn-sum", earn_sum, this.earn_sum, 2, 2).start();
                    new CountUp("consumption-sum", consumption_sum, this.consumption_sum, 2, 2).start();
                    new CountUp("balance-sum", (earn_sum-consumption_sum), (this.earn_sum - this.consumption_sum), 2, 2).start();
                    localStorage.muchmoney=this.earn_sum - this.consumption_sum;
                });
            },
            onScroll (pos) {
                this.scrollTop = pos.top;
            },
            onPullDownLoading () {
                this.$nextTick(() => {
                    this.$refs.billScrollEvent.reset();
                    this.$refs.billScrollEvent.donePulldown();
                });
            },
            onPullUpLoading () {
                this.$nextTick(() => {
                    this.$refs.billScrollEvent.reset();
                    this.$refs.billScrollEvent.donePullup();
                })
            }
        }
    }
</script>
<style lang="scss">
    @import "../../assets/scss/define";
    .bbb{
        background-image:url('../../../static/img/shining4.gif');
        background-size: cover;
        height: 575px;
        // background-color: #f7cc4c;
    }
    .menu-type-wrap{
        @extend %clearfix;
        margin-top: 10px;
    }
    .bill-null-warp{
        @extend %pa;
        @extend %c9;
        @extend %l0;
        @extend %tac;
        @extend %r0;
        padding-top: 100px;
    }
    .bill-list-null-icon{
        @extend %db;
        @extend %ma;
        width: 135px;
        height: 100px;
        fill: #fff;
    }
    .dialog-con{
        padding: 20px 0;
    }
    .bill-dialog-box{
        background-color: #F9FAFC;
    }
    .bill-dialog-close{
        @extend %pa;
        top: 8px;
        right: 8px;
        width: 15px;
        height: 15px;
        &:after,
        &:before{
            content: '';
            @extend %pa;
            @extend %l0;
            top: 7px;
            width: 15px;
            height: 1px;
            background-color: #999;
            -webkit-transform: rotate(-45deg);
            transform: rotate(-45deg);
        }
        &:after{
            -webkit-transform: rotate(45deg);
            transform: rotate(45deg);
        }
    }
    .bill-dialog-ok{
        @extend %pr;
        @extend %dib;
        @extend %vam;
        @extend %c9;
        margin: 8px 0;
        width: 24px;
        height: 24px;
        &:after,
        &:before{
            content: '';
            @extend %pa;
            @extend %l0;
            top: 7px;
            height: 1px;
            background-color: #58B7FF;
            -webkit-transform: rotate(-45deg);
            transform: rotate(-45deg);
        }
        &:after{
            left: -5px;
            top: 13px;
            width: 13px;
            -webkit-transform: rotate(45deg);
            transform: rotate(45deg);
        }
        &:before{
            width: 24px;
            top: 9px;
            left: 2px;
        }
    }
    .bill-reduce,
    .bill-equal{
        width: 12px;
        margin: 0 4px;
    }
    
    .bill-sum-earn{
        color: #000;
    }
    .bill-sum-consumption{
        color: #fff;
    }
    .bill-sum-balance{
        color: #fff;
    }
    .bill-reduce{
        height: 2px;
        margin-top: 22px;
        background-color: #58B7FF;
    }
    .bill-equal{
        height: 6px;
        margin-top: 18px;
        border-bottom: 2px solid #58B7FF;
        border-top: 2px solid #58B7FF;
    }
    .bill-prompt-wrap{
        @extend %pa;
        @extend %r0;
        @extend %l0;
        @extend %df;
        top:0px;
        height: 160px;
        background-image:url('../../../static/img/tiger4.jpg');
        background-size: cover;
        background-color: #f7cc4c;
    }
    .bill-sum{
        @extend %pa;
        @extend %tac;
        @extend %r0;
        @extend %l0;
        @extend %fwb;
        top:16px;
        color:black;
        font-size:24px;
        line-height: 35px;
    }
    .bill-sum2{
        @extend %pa;
        @extend %tac;
        @extend %r0;
        @extend %l0;
        @extend %fwb;
        top:30px;
        color:black;
        font-size:32px;
        line-height: 35px;
    }
    .bill-sum-title{
        @extend %pa;
        @extend %tac;
        @extend %r0;
        @extend %l0;
        font-size:20px;
        top: 19px;
        left:-135px;
        color: #000;
    }
    .bill-sum-title2{
        @extend %pa;
        @extend %tac;
        @extend %r0;
        @extend %l0;
        font-size:20px;
        top: -4px;
        color: #000;
    }
    .bill-prompt1{
        position:absolute;
        padding-top: 15px;
        left:9%;
        top:35px;
        width:80px;
        height:75px;
    }
    .bill-prompt2{
        position:absolute;
        bottom:0px;
        width:80px;
        height:60px;
        left:85px;
        padding-top: 10px;
    }
    .bill-prompt3{
        position:absolute;
        bottom:0px;
        left:300px;
        width:80px;
        height:60px;
        padding-top: 10px;
    }
    .bill-cancel{
        @extend %pa;
        @extend %f16;
        @extend %cfff;
        @extend %cp;
        width:30px;
        height:30px;
        line-height: 30px;
        border-radius: 15px;
        margin-left:-32px;
        text-align: center;
        right:15px;
        bottom: 20px;
        background-color: #FF4949;
    }
    .bill-type-check-item{
        @extend %pr;
        @extend %fl;
        width: 20%;
        padding-bottom: 20%;
        &.bill-type-check-item-selected{
            .checker-item-icon{
                fill: #58B7FF;
            }
        }
    }
    .checker-item-icon{
        @extend %pa;
        @extend %t50;
        @extend %l50;
        width: 30px;
        height: 30px;
        margin-top: -15px;
        margin-left: -15px;
        fill: #ccc;
        transition: all .5s;
    }
    .bill-wrap{
        @extend %oya;
        @extend %pa;
        @extend %w100;
        @extend %b0;
        top: 144px;
        background-image:url('../../../static/img/shining4.gif');
        background-size: cover;
    }
    .bill-filter{
        @extend %pa;
        @extend %cp;
        z-index:10;
        top: 20px;
        right: 20px;
        width: 20px;
        height: 20px;
        fill: #fff;
    }
    .container-box{
        @extend %h100;
        transition: all .5s;
        &.open-menu{
            transform: translate3d(-82%,0,0);
        }
    }
    .filter-menu{
        @extend %pa;
        @extend %t0;
        @extend %r0;
        @extend %b0;
        transition: all .5s;
        transform: translate3d(100%,0,0);
        width: 82%;
        z-index: 1;
        background-color: #fff;
        &:before{
            content: " ";
            @extend %pa;
            @extend %l0;
            @extend %t0;
            @extend %b0;
            width: 1px;
            background-color: #999;
            -webkit-transform-origin: 0 0;
            transform-origin: 0 0;
            -webkit-transform: scaleX(0.5);
            transform: scaleX(0.5);
        }
    }
    .filter-menu-item{
        @extend %db;
        @extend %f12;
        @extend %cp;
        padding-left: 10px;
        line-height: 30px;
    }
    .menu-btn-wrap{
        @extend %pa;
        @extend %df;
        @extend %b0;
        @extend %l0;
        @extend %r0;
    }
    .menu-btn{
        @extend %df1;
        @extend %f14;
        @extend %tac;
        @extend %cfff;
        @extend %cp;
        height: 40px;
        line-height: 40px;
    }
    .menu-sure-btn{
        background-color:#7e57c2;
    }
    .menu-reset-btn{
        background-color: #ccc;
    }
    .bill-list{
        margin: 6px 10px 20px;
        background-color:rgba(251,227,197,0);
        border-radius:10px;
        padding-top:10px;
    }
    .bill-item{
        @extend %pr;
        margin-top:12px;
        margin-left: 3px;
        padding: 10px;
        height:200px;
        background-image:url('../../../static/img/tag5.png');
        border:6px solid #a07fda;
        border-radius:20px;
        background-size: cover;
        box-shadow: 0 0 0 1px hsla(0,0%,100%,.3) inset,0 .5em 1em rgba(10, 10, 10, 0.6);
        &:last-child{
            border-bottom: none;
        }
    }
    .bill-item-null{
        padding: 20px 0;
    }
    .bill-item-type{
        @extend %pa;
        top:22%;
        left: 21px;
        width: 80px;
        height: 88px;
        margin-top: -25px;
        background-image:url('../../../static/img/tag1.png');
        background-size: cover;
        &.earn-type{
            fill: #13CE66;
        }
        &.consumption-type{
            fill: #FF4949;
        }
    }
    .bill-item-type-icon{
        @extend %pa;
        top:45%;
        @extend %l50;
        width: 33px;
        height: 33px;
        margin-top: -16px;
        margin-left: -17px;
    }
    .bill-item-con{
        font-size:40px;
        @extend %fwb;
        @extend %oh;
        @extend %c3;
        padding:3px;
        margin-top:21px;
        line-height: 40px;
        text-align:center;
    }
    .bill-item-remark{
        @extend %pa;
        bottom:10px;
        width:100%;
        text-align:center;
        font-size:15px;
        // font-weight: bold;
    }
    .bill-item-sum{
        @extend %fl;
        @extend %pr;
        margin-left:158px;
        padding-right: 40px;
        &:after{
            @extend %pa;
            @extend %r0;
            content: '￥';
        }
    }
    .bill-item-time{
        @extend %oh;
        @extend %tac;
        @extend %f16;
        @extend %c9;
        margin-top:10px;
        height: 20px;
        line-height: 20px;
        margin-left:47px;
    }
</style>
