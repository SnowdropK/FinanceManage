<template>
    <div class="container-view">
        <head-title :title="'理财管家'"></head-title>
        <div class="chart-wrap" style="top:42px;">
            <scroller lock-x
                      height="-118"
                      :scrollbarY="true"
                      @on-scroll="onScroll"
                      ref="chartScrollEvent"
                      style=""
                      class="sss"
                      >
                <div class="chart-con-maid">
                    <div class="messageBox"></div>
                </div>
            </scroller>
        </div>
        <div class='inputBox' >
            <textarea class='messageText'></textarea>
            <!-- <input class='sendbtn' type="button" value='发送' v-on:click="SendBtn()" v-on:keyup.13="SendBtn"/> -->
            <mu-raised-button label="发送" class="demo-raised-button sendbtn" id="demo_change" v-on:click="SendBtn()" primary style=""/>
        </div>
        <!-- <div class="speechJump"><a href="#/speech">语音</a></div> -->
        <mu-float-button id="autoJump" icon="☺" class="demo-float-button" href="#/speech"/>
    </div>
</template>
<script>
    import types from '../../store/mutation-types'
    import headTitle from '../../components/head-title.vue'
    import Util from '../../assets/lib/Util'
    import Tool from '../../assets/lib/Tool'
    import bc from '../../../static/img/bc.jpg'
    import { Scroller } from 'vux'
    export default {
        name: 'maid',
        data () {
            return {
                consumption_chart_arr: [0,0,0,0,0,0,0,0],
                earn_chart_arr: [0,0,0],
                bc: bc
            }
        },
        components: {
            Scroller,
            headTitle
        },
        mounted () {
            this.StoreInit();
        },
        methods: {
            StoreInit(){
                //1、获取本地存储的对话内容
                    var storeChat;
                    if(sessionStorage.storeChat==undefined){
                        sessionStorage.storeChat="主人，萌萌酱正在为您服务！";
                        storeChat=sessionStorage.storeChat;
                    //初始问候声音
                        var soundstart = speechSynthesis.getVoices();
                        var meng = new window.SpeechSynthesisUtterance(storeChat);
                        meng.pitch = localStorage.speech_pitch;
                        meng.rate = localStorage.speech_rate;
                        window.speechSynthesis.speak(meng);
                    }
                    else{
                        storeChat=sessionStorage.storeChat;
                    }                
                //2、存储的字符串转化为数组
                    var storeChatArray=storeChat.split('#');   
                //3、遍历数组输出
                    for(var i=0;i<storeChatArray.length;i++){
                        if(i%2==1){
                            var createDivOld = document.createElement('div');
                            createDivOld.className = 'self';
                            createDivOld.innerText = storeChatArray[i];
                            // 添加到messageBox 中
                            document.querySelector('.messageBox').appendChild(createDivOld);


                        }
                        else{
                            var otherDivOld = document.createElement('div');
                            otherDivOld.className = 'other';
                            otherDivOld.innerText = storeChatArray[i];
                            // 添加到messageBox 中
                            document.querySelector('.messageBox').appendChild(otherDivOld);
                        }
                    }
            },
            SendBtn(){
                //获取消费数据
                var bill_maid2=JSON.parse(localStorage.bill_maid);
                var bill_type=[0,0,0,0,0];
                console.log(bill_maid2);
 //console.log(bill_type[4]);
                for(var i=0;i<bill_maid2.length;i++){
                    if(bill_maid2[i].bill_account_type[0]=="水果零食" || bill_maid2[i].bill_account_type[0]=="餐饮伙食")
                        bill_type[0]=bill_type[0]+parseInt(bill_maid2[i].bill_sum);
                    else if(bill_maid2[i].bill_account_type[0]=="生活日常" || bill_maid2[i].bill_account_type[0]=="租房水电")
                        bill_type[1]=bill_type[1]+parseInt(bill_maid2[i].bill_sum);
                    else if(bill_maid2[i].bill_account_type[0]=="出行旅游")
                        bill_type[2]=bill_type[2]+parseInt(bill_maid2[i].bill_sum);
                    else if(bill_maid2[i].bill_account_type[0]=="医疗药物")
                        bill_type[3]=bill_type[3]+parseInt(bill_maid2[i].bill_sum);
                    else if(bill_maid2[i].bill_account_type[0]=="网上购物" || bill_maid2[i].bill_account_type[0]=="其他消费")
                        bill_type[4]=bill_type[4]+parseInt(bill_maid2[i].bill_sum);
                }

                localStorage.bill_typelist=JSON.stringify(bill_type);
                //console.log(localStorage.bill_typelist);


                //4、对话内容
                    var storeChatString=sessionStorage.storeChat;
                // 获取 用户输入的 文本框的值
                    var inputValue = document.querySelector('.messageText').value;
                    storeChatString=storeChatString+"#"+inputValue;
                // 创造声音
                    var allsound = speechSynthesis.getVoices();

                // 创建 div  设置class 为 self,将输入的内容设置进去
                    var createDiv = document.createElement('div');
                    createDiv.className = 'self';
                    
                    createDiv.innerText = inputValue;
                // 添加到messageBox 中
                    document.querySelector('.messageBox').appendChild(createDiv); 
                    var that=this;
                    setTimeout(function(){                      
                        var otherDiv = document.createElement('div');
                        otherDiv.className = 'other';
                        var moneynum=parseInt(localStorage.muchmoney);
                        var bill_typelistn=JSON.parse(localStorage.bill_typelist);
                        //支出总金额
                        var bill_typelistsum=0;
                        for(var i=0;i<5;i++){
                            bill_typelistsum=bill_typelistsum+bill_typelistn[i];
                        }
                        console.log(bill_typelistsum);
                        if(inputValue=="收支情况"){
                            if(moneynum<=-100)
                                otherDiv.innerText = "我的天，主人，我实在不想讲出来，你非但没挣钱，还花了"+-moneynum+"元！你要好好反省自己！";
                            else if(moneynum<0 && moneynum>-100)
                                otherDiv.innerText = "主人，你现在的收入是赤字。你让我怎么说你才好，竟然倒贴了"+-moneynum+"元！";
                            else if(moneynum>0 && moneynum<200)
                                otherDiv.innerText = "主人，你还算努力，目前赚了"+moneynum+"元！";
                            else if(moneynum>=200)
                                otherDiv.innerText = "哇，主人，萌萌酱为你开心，你竟然赚了"+moneynum+"元！";
                            else
                                otherDiv.innerText = "主人，你很佛系，没有挣到钱，但也没有花钱。";
                        }
                        else if(inputValue=="income and expenditure"){
                            if(localStorage.muchmoney<=-100)
                                otherDiv.innerText = "My God, Master, I really don’t want to say it. Not only did you not make money, it took "+Math.abs(moneynum)+" yuan! You have to reflect on yourself!";
                            else if(moneynum<0 && moneynum>-100)
                                otherDiv.innerText = "Master, your current income is a deficit. How do you let me say you? I turned it down "+-moneynum+" yuan!";
                            else if(moneynum>0 && moneynum<200)
                                otherDiv.innerText = "Master, you're still working hard, currently earning "+moneynum+" yuan!";
                            else if(moneynum>=200)
                                otherDiv.innerText = "Wow, master, Meng Meng jiang is happy for you, you actually earned "+moneynum+" yuan!";
                            else
                                otherDiv.innerText = "Master, you are a Buddha, and you have not earned money, but you have not spent money.";
                        }
                        else if(inputValue=="我想和你聊聊"){
                            otherDiv.innerText = "滚犊子，老娘不约！";
                        }
                        else if(inputValue=="我应该怎么省钱"){
                            if(bill_typelistn[0] / bill_typelistsum > 0.5){
                                otherDiv.innerText = "主人，你这个吃货，大部分钱都花在买吃的上了，在吃东西上花了"+bill_typelistn[0]+"元！";
                            }
                            else if(bill_typelistn[1] / bill_typelistsum > 0.5){
                                otherDiv.innerText = "主人，你的消费观比较保守，大部分钱用于生活日常和租房水电，总共花了"+bill_typelistn[1]+"元！真贤惠！";
                            }
                            else if(bill_typelistn[2] / bill_typelistsum > 0.5){
                                otherDiv.innerText = "主人，你最近有点闲，根据消费记录，你经常出去旅游，旅游消费是"+bill_typelistn[2]+"元！";
                            }
                            else if(bill_typelistn[3] / bill_typelistsum > 0.5){
                                otherDiv.innerText = "主人，要注意身体啊，最近药买的有些多，消费了"+bill_typelistn[3]+"元！是不是身体不好。";
                            }
                            else if(bill_typelistn[4] / bill_typelistsum > 0.5){
                                otherDiv.innerText = "主人，你太会买买买了，钱都花在网购上，都已经花了"+bill_typelistn[4]+"元！你个败家子。";
                            }
                            else{
                                otherDiv.innerText = "主人，你这阵子一分钱都没花，是不是在吃土。";
                            }
                        }
                        else{
                            otherDiv.innerText = "主人你在说什么，我怎么听不懂？";
                        }

                    var utterThis = new window.SpeechSynthesisUtterance(otherDiv.innerText);
                    utterThis.pitch = localStorage.speech_pitch;
                    utterThis.rate = localStorage.speech_rate;
                    window.speechSynthesis.speak(utterThis);

                // 添加到messageBox 中おはようございます
                    document.querySelector('.messageBox').appendChild(otherDiv);
                    

                //5、字符串拼接
                    storeChatString=storeChatString+"#"+otherDiv.innerText;
                   
                //6、数组变字符串存入sessitionStorage
                    sessionStorage.storeChat=storeChatString;
                },300);
            },
            onScroll (pos) {
                this.scrollTop = pos.top;
            }
        }
    }
</script>
<style lang="scss">
    @import "../../assets/scss/define";
    .sss{
        background-image:url('../../../static/img/shining6.gif');
        background-size: cover;
        padding:10px 15px;
        height: 641px!important;
    }
    .container {
        height: 100%;
        width: 100%;
        margin: 0 auto;
        flex-direction: column;
    }
          
    .messageBox {
        padding:5px 5px 103px;
        border-radius:6px;
        background-color:rgba(255,255,255,0.3);
        box-shadow: 0 0 0 1px hsla(0,0%,100%,.3) inset,0 .5em 1em rgba(0, 0, 0, 0.6);
        text-shadow: 0 1px 1px hsla(0,0%,100%,.3);
    }  
    .inputBox {
        position:absolute;
        bottom:0px;
        padding:2px;
        width:100%;
        height:40px;
        background: #c4afe9;
        overflow:hidden;
    }
    .speechJump{
        position:absolute;
        top:3px;
        right:3px;
        padding:2px;
        width:40px;
        height:40px;
        background: green;
    }  
    #autoJump{
        position:absolute;
        top:14px;
        right:5px;
    }   
    .messageText {
        font-size: 30px;
        font-size:16px;
        height:100%;
        width:75%;
        margin-left:3px;
        padding-left: 10px;
    }
    .mu-ripple-wrapper{
        width:10px;
    }      
    .sendbtn {
        position:relative;
        display: inline-block;
        font-size: 14px;
        border-width: 0px;
        background: hotpink;
        top:-12px;
        text-align: center;
        width:10px;
    }
    #demo_change{
        min-width:0px;
        width: 70px;
        height:34px;
        position: relative;
        font-size: 14px;
        top:-13px;
    }    
    .self {
        width:110px;
        margin-top:5px;
        margin-left:259px;
        position:relative;   
        padding:9px 7px;  
        background:rgb(248, 247, 241);  
        border:4px solid #F8C301;
        border-radius:12px; /* 圆角 */ 
        font-size:16px; 
        text-align:left;
        /* 
        border-top:1px solid  rgba(255,192,203,1);
        border-right:8px solid  rgba(2,19,203,1);
        border-bottom:1px solid  rgba(255,192,203,1);
        border-left:1px solid  rgba(255,192,203,1);
        background-image: url('../../../static/img/arrow_right.png');
        background-repeat: no-repeat;
        background-position: 123px 10px;
        */
    }
    // .self::after{
    //     position:absolute;  
    //     top:8px;  
    //     right:-16px; /* 圆角的位置需要细心调试哦 */  
    //     width:0;  
    //     height:0;     
    //     border-top:8px solid  rgba(77,73,72,0);
    //     border-right:8px solid  rgba(77,73,72,0);
    //     border-bottom:8px solid  rgba(77,73,72,0);
    //     border-left:8px solid  rgba(255,192,203,1);
    // }
         
    .other {
        margin-top:5px;
        position:relative; 
        margin-left:6px; 
        width:163px;  
        padding:9px 7px;  
        background:#f9f7fc; 
        border:4px solid #7e57c2; 
        border-radius:12px; /* 圆角 */ 
        font-size:16px; 
    }
    // .other::after{  
    //     position:absolute;  
    //     top:8px;  
    //     left:-16px; /* 圆角的位置需要细心调试哦 */  
    //     width:0;  
    //     height:0;     
    //     border-top:8px solid  rgba(77,73,72,0);
    //     border-right:8px solid  rgba(255,192,203,1);
    //     border-bottom:8px solid  rgba(77,73,72,0);
    //     border-left:8px solid  rgba(77,73,72,0);
    // } 
</style>
