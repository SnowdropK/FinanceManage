<template>
    <div class="container-view">
        <div class="home-wrap"
            :class="{'home-active': is_open}" id="home_bc">
            <!-- 月亮 -->
            <div class="moon" id="moon">
                <ul>
                    <li></li>
                    <li></li>
                    <li></li>
                </ul>
                <!-- <svg class="balance-icon">
                    <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#type-jbgz"></use>
                </svg> -->
                <h2 class="balance-title">可用余额</h2>
                <h1 class="balance-total" id="total_balance" style="font-size:28px;">
                    <spinner type="ios" slot="value"></spinner>
                </h1>
            </div>
            <!-- 雪花 -->
            <div class="flakes1">
	            <p>*</p>
	        </div>
	        <div class="flakes2">
	            <p>*</p>
	        </div>
            <!-- 月亮 -->
            <div id="iTwo" style="width: 150px; height: 150px; border-radius:75px; background-color: rgba(157, 229, 253,0.4);"></div>   
            <div id="iOne" style="width: 150px; height: 150px; border-radius:75px; background-color: rgba(157, 229, 253,0.4);"></div>  
            
            <div class="home-btn-wrap">
                <a href="#/modify" class="go-account go-earn sparkley">修改密码</a>
                <i @click="is_popup = true" class="go-account go-consumption">安全退出</i>
            </div>
            <svg @click="is_open = true" class="home-arrow" v-show="!is_open">
                <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#nav-arrow"></use>
            </svg>

            <!--弹窗提示-->
            <x-dialog v-model="is_popup" class="dialog-demo" hide-on-blur>
                <div class="dialog-con">
                    确定要退出吗？
                </div>
                <span class="bill-dialog-close"  @click="is_popup = false"></span>
                <div class="bill-dialog-box" @click="safeExit()">
                    <span class="bill-dialog-ok"></span>
                </div>
            </x-dialog>
            <!--/弹窗提示-->
        </div>
    </div>
</template>
<script>
    import { Scroller, Spinner, XDialog} from 'vux'
    import GestureMobile from '../../assets/lib/GestureMobile'
    import types from '../../store/mutation-types'
    import CountUp from '../../assets/lib/countUp'
    import Util from '../../assets/lib/Util'
    import Tool from '../../assets/lib/Tool'
    import $ from 'jquery'
    export default {
        name: 'home',
        data: function () {
            return {
                is_open: false,
                total_balance: 0,
                is_popup: false
            }
        },
        components:{
            Scroller,
            Spinner,
            XDialog
        },
        created () {
            this.fetchBalance();
            this.gestureMobile();
            this.setNavIndex();
        },
        mounted(){
        },
        methods: {
            /**安全退出*/
            safeExit () {
                this.is_popup = false;
                Tool.dataToSessionStorageOperate.clear();
                this.$router.push('/login');
            },
            /**手势*/
            gestureMobile () {
                this.$nextTick(() => {
                    let _this = this;
                    GestureMobile(this.$el,{
                        upCallBackFun () {
                            _this.is_open = true;
                        },
                        downCallBackFun () {
                            _this.is_open = false;
                        }
                    });
                })
            },
            /**获取可用余额*/
            fetchBalance () {
                var user_name = Tool.dataToSessionStorageOperate.achieve('user').user_name;
                Util.fetchTotalBalance(user_name,(result) => {
                    this.total_balance = result.data.balance;
                    setTimeout( () => {
                        this.$nextTick(() => {
                            new CountUp("total_balance", 0, this.total_balance, 2, 2).start();
                        })
                    },200)
                });
            },
            /**设置导航条按钮状态*/
            setNavIndex () {
                this.$store.commit(types.SET_NAV_INDEX,'1')
            }
        }
    }
</script>
<style lang="scss">
    @import "../../assets/scss/define";
    #home_bc{
        background-image:url('../../../static/img/snow.jpg');
        background-size: cover;
        bottom:0px;
    }
    .home-active{
        .moon{
            top: 25%;
        }
        #iTwo{
            top: 14%;
        }
        #iOne{
            top: 14%;
        }
        .home-btn-wrap{
            bottom: 15%;
            opacity: 1;
        }
    }
    .moon{
        @extend %pa;
        @extend %oh;
        @extend %l50;
        @extend %t50;
        // width: 50%;
        transition: all 2s;
        transform: translate3d(-50%,-50%,0);
        // padding-bottom: 50%;
        // border-radius: 50%;
        width: 150px;  
        height: 150px; 
        border-radius:75px; 
        background-color: #fff;
        z-index:10;
        box-shadow: 0px 0px 100px rgba(157, 229, 253, 0.69);
    }
    #iOne{      
        transition: all 2s;     
        animation: animateOne 4s infinite ease;
        @extend %pa;
        left:32%;
        top:39%;  
    }     
    #iTwo{  
        transition: all 2s;     
        animation: animateTwo 4s infinite ease; 
        @extend %pa;
        left:32%;
        top:39%;  
    }   
    @keyframes animateOne {     
        from{              
  			background-color: rgba(157, 229, 253,0.4) ;   //157, 229, 253
         }        
    	to{             
   			background-color: transparent;   
            transform:scale(1.8)        
    	} 
    }   
    @keyframes animateTwo {     
        from{              
  			background-color: rgba(157, 229, 253,0.6) ;   
         }        
    	to{             
   			background-color: transparent;   
            transform:scale(1.3)        
    	} 
    }

    .balance-title{
        @extend %pa;
        @extend %f18;
        @extend %fwn;
        @extend %tac;
        @extend %l0;
        @extend %r0;
        color: #999;
        top: 30%;
    }
    .balance-total{
        @extend %pa;
        @extend %fwn;
        @extend %f22;
        @extend %tac;
        @extend %l0;
        @extend %r0;
        @extend %t50;
        height: 48px;
        margin-top: -15px;
        line-height: 48px;
        font-size: 24px;
        color: #13CE66;
        &:after{
            @extend %pa;
            @extend %f12;
            @extend %b0;
            line-height: 42px;
            content: '(￥)';
            color: #999;
        }
        .vux-spinner{
            line-height: 28px;
        }
    }
    .balance-icon{
        @extend %pa;
        top: 25%;
        left: 25%;
        width: 50%;
        height: 50%;
        fill: #ddd;
    }
    .home-btn-wrap{
        @extend %pa;
        @extend %r0;
        @extend %l0;
        @extend %tac;
        @extend %f14;
        @extend %cfff;
        opacity: 0;
        transition: all 1.5s;
        bottom: -30%;
    }
    .home-btn-item{
        @extend %db;
        @extend %cp;
        @extend %cfff;
        margin: 20px auto 0;
        width: 120px;
        height: 32px;
        line-height: 32px;
        background-color: #58B7FF;
        border-radius: 5px;
        box-shadow: 0 3px 0 0 #1D8CE0;
    }
    .home-arrow{
        @extend %pa;
        @extend %l50;
        margin-left: -10px;
        bottom: 20px;
        width: 20px;
        height: 20px;
        fill: #999;
        animation: arrow-animate 2s ease-in-out infinite;
    }
    @keyframes arrow-animate {
        0%{
            bottom: 10px;
        }
        50%{
            bottom: 20px;
        }
        100%{
            bottom: 10px;
        }
    }


    // 雪花特效 
    .flakes1 {
        width: 100%;
        height: 100%;
        position: absolute;
        top: 0px;
        color: #fff;
        opacity: 0.5;
        -webkit-animation: sfanim linear 25s;
        animation: sfanim linear 25s;
        -webkit-animation-iteration-count: infinite;
        animation-iteration-count: infinite;
        text-shadow: 303px 117px, 32px 89px, 323px 119px, 98px 183px, 126px 235px, 0px 171px, 380px 61px, 269px 17px, 0px 151px, 121px 344px, 229px 136px, 237px 280px, 303px 30px, 211px 314px, 378px 285px, 10px 287px, 93px 345px, 292px 324px, 223px 292px, 156px 160px, 253px 58px, 205px 195px, 145px 106px, 79px 312px, 182px 359px, 279px 99px, 349px 124px, 5px 33px, 216px 147px, 388px 117px, 70px 295px, 149px 318px, 96px 66px, 129px 217px, 138px 218px, 241px 310px, 231px 368px, 18px 327px, 173px 213px, 118px 10px, 246px 208px, 159px 244px, 268px 376px, 167px 262px, 85px 238px, 277px 47px, 386px 192px, 259px 364px, 325px 327px, 279px 201px, 303px 517px, 32px 489px, 323px 519px, 98px 583px, 126px 635px, 0px 571px, 380px 461px, 269px 417px, 0px 551px, 121px 744px, 229px 536px, 237px 680px, 303px 430px, 211px 714px, 378px 685px, 10px 687px, 93px 745px, 292px 724px, 223px 692px, 156px 560px, 253px 458px, 205px 595px, 145px 506px, 79px 712px, 182px 759px, 279px 499px, 349px 524px, 5px 433px, 216px 547px, 388px 517px, 70px 695px, 149px 718px, 96px 466px, 129px 617px, 138px 618px, 241px 710px, 231px 768px, 18px 727px, 173px 613px, 118px 410px, 246px 608px, 159px 644px, 268px 776px, 167px 662px, 85px 638px, 277px 447px, 386px 592px, 259px 764px, 325px 727px, 279px 601px;
    }
    .flakes2 {
        width: 100%;
        height: 100%;
        position: absolute;
        top: 0px;
        color: #fff;
        -webkit-animation: sfanim linear 16s;
        animation: sfanim linear 16s;
        -webkit-animation-iteration-count: infinite;
        animation-iteration-count: infinite;
        text-shadow: 375px 485px, 11px 689px, 254px 784px, 5px 686px, 266px 705px, 388px 698px, 180px 707px, 36px 413px, 74px 695px, 238px 690px, 384px 635px, 1px 694px, 45px 538px, 131px 750px, 258px 520px, 157px 705px, 96px 749px, 325px 719px, 132px 688px, 167px 511px, 303px 408px, 340px 620px, 394px 428px, 187px 748px, 217px 624px, 356px 630px, 33px 758px, 238px 762px, 357px 586px, 253px 798px, 68px 786px, 164px 662px, 119px 598px, 221px 557px, 126px 537px, 282px 503px, 11px 455px, 219px 632px, 60px 597px, 41px 529px, 247px 451px, 217px 644px, 304px 400px, 214px 421px, 287px 757px, 76px 404px, 376px 735px, 169px 572px, 245px 790px, 66px 717px, 375px 85px, 11px 289px, 254px 384px, 5px 286px, 266px 305px, 388px 298px, 180px 307px, 36px 13px, 74px 295px, 238px 290px, 384px 235px, 1px 294px, 45px 138px, 131px 350px, 258px 120px, 157px 305px, 96px 349px, 325px 319px, 132px 288px, 167px 111px, 303px 8px, 340px 220px, 394px 28px, 187px 348px, 217px 224px, 356px 230px, 33px 358px, 238px 362px, 357px 186px, 253px 398px, 68px 386px, 164px 262px, 119px 198px, 221px 157px, 126px 137px, 282px 103px, 11px 55px, 219px 232px, 60px 197px, 41px 129px, 247px 51px, 217px 244px, 304px 0px, 214px 21px, 287px 357px, 76px 4px, 376px 335px, 169px 172px, 245px 390px, 66px 317px;
        }
        @-webkit-keyframes sfanim {
            0% {
                -webkit-transform: translate(0px, -400px);
                transform: translate(0px, -400px);
            }
            100% {
                -webkit-transform: translate(0px, 0px);
                transform: translate(0px, 0px);
            }
        }
        @keyframes sfanim {
            0% {
                -webkit-transform: translate(0px, -400px);
                transform: translate(0px, -400px);
            }
            100% {
                -webkit-transform: translate(0px, 0px);
                transform: translate(0px, 0px);
            }
        }

        // 按钮星星
</style>
