<template>
    <div class="container-view login-wrap" id="speech_bc">
        <head-title :title="'设置声音变量'"></head-title>
        <!-- <div class="speechJump2"><a href="#/maid">聊天室</a></div> -->
        <mu-float-button icon="回"  id="demoJump" class="demo-float-button" href="#/maid"/>
        <form>
            <!-- <input type="text" class="txt"> -->
            <mu-text-field hintText="请在这里输入测试文字" multiLine :rows="3" :rowsMax="6"/><br/>
            <!-- 频率调节 -->
            <div>
                <label for="rate">频率</label><input type="range" min="0.5" max="2" value="1" step="0.1" id="rate">
                <div class="rate-value">1</div>
                <div class="clearfix"></div>
            </div>
            <!-- 音调高低调节 -->
            <div>
                <label for="pitch">音调</label><input type="range" min="0" max="2" value="1" step="0.1" id="pitch">
                <div class="pitch-value">1</div>
                <div class="clearfix"></div>
            </div>
            <!-- 语言选择按钮 -->
          <!-- <select></select> -->

            <!-- 演示按钮 -->
          <div class="controls">
              <!-- <button id="play" type="submit" v-on:click="inithua()">测试</button> -->
              <mu-raised-button label="测试" id="maid_img" v-on:click="inithua()" class="demo-raised-button" primary/>
          </div>
          <div class="cutemaid">
              <img :src="imgsrc"/>
          </div>
          <div class="maidword">现在是初始化状态下的声音</div>
        </form>
        
    </div>
</template>
<script>
    import headTitle from '../../components/head-title.vue'
    import types from '../../store/mutation-types'
    import Util from '../../assets/lib/Util'
    import Tool from '../../assets/lib/Tool'
    import { XInput } from 'vux'
    export default {
        name: 'speech',
        data () {
            return {
                old_password_value: '',
                password_value: '',
                too_password_value: '',
                is_timer: false,
                imgsrc: require('../../../static/img/maid1.jpg')
            }
        },
        methods: {
            inithua(){
                //创建声音
	            var synth = window.speechSynthesis;
                //获取表格
	            var inputForm = document.querySelector('form');
	            //获取输入的文字
                var inputTxt = document.querySelector('.mu-text-field-input');
                //音调高低拉条
	            var pitch = document.querySelector('#pitch');
	            //高低的1
	            var pitchValue = document.querySelector('.pitch-value');
	            //频率高低拉条
	            var rate = document.querySelector('#rate');
	            //频率的1
                var rateValue = document.querySelector('.rate-value');
                //获取图片
                var  maid_img= document.querySelector('.maid_img');
                //获取图片文字
                var  maidword= document.querySelector('.maidword');
                
                //最最核心就是下面四段代码，获取输入内容，改频率和音调
                var utterThis = new SpeechSynthesisUtterance(inputTxt.value);
	            utterThis.pitch = pitch.value;
	            utterThis.rate = rate.value;
                synth.speak(utterThis);
                
                //声音提示
                if(rate.value<1 && pitch.value<1){
                    maidword.innerHTML="是很磁性的声音哦！";
                    this.imgsrc=require('../../../static/img/maid2.jpg')
                }
                else if(rate.value<1 && pitch.value>1){
                    maidword.innerHTML="很有干劲的声音哦！";
                    this.imgsrc=require('../../../static/img/maid3.jpg')
                }
                else if(rate.value==1 && pitch.value==1){
                    maidword.innerHTML="比较正常的声音";
                    this.imgsrc=require('../../../static/img/maid4.jpg')
                }
                else if(rate.value>1 && pitch.value<1){
                    maidword.innerHTML="在正式场合的声音";
                    this.imgsrc=require('../../../static/img/maid5.jpg')
                }
                else if(rate.value>1 && pitch.value>1){
                    maidword.innerHTML="十分朝气的声音呢！";
                    this.imgsrc=require('../../../static/img/maid6.jpg')
                }

                //声音高低和频率存在localStorage里面，在Maid.vue里读取
                localStorage.speech_pitch = pitch.value;
                localStorage.speech_rate = rate.value;

                //改掉值
                 pitchValue.textContent = pitch.value;
                 rateValue.textContent = rate.value;
            }
        },
        components: {
            headTitle,
            XInput
        }
    }
</script>
<style lang="scss">
    @import "../../assets/scss/define";
    #speech_bc{
        
    }
    .cutemaid{
        width:156px;
        height:176px;
        margin-top:50px;
        // border:1px solid red;
    }
    .cutemaid img{
        width:140px;
        height:168px;
    }
    .maidword{
        width: 240px;
        height: 80px;
        text-align:center;
        font-size:24px;
        // border:1px solid red;
        margin:0 auto;
        color:#7e57c2;
        
    }
    .mu-text-field-line,.mu-text-field-focus-line{
        width:85%;
        left:18px !important;
    }
    .mu-text-field{
        margin-left:85px;
        padding:15px;
        border-radius: 15px;
        background-color: #fff;
        border:4px solid #7e57c2;
    }
    .speechJump2{
        position:absolute;
        bottom:33px;
        right:3px;
        padding:2px;
        width:40px;
        height:40px;
        background: green;
    }
    #demoJump{
        position:absolute;
        bottom:33px;
        right:5px;
    }
    .txt, select, form > div {
    display: block;
    margin: 0 auto;
    font-family: sans-serif;
    font-size: 16px;
    padding: 5px;
    }

    .txt {
    width: 85%;
    }
    select {
    width: 83%;
    }

    form > div {
    width: 85%;
    }

    .txt, form > div {
    margin-bottom: 10px;
    overflow: auto;
    }

    .clearfix {
    clear: both;
    }

    label {
    float: left;
    width: 10%;
    line-height: 1.5;
    }

    .rate-value, .pitch-value {
    float: right;
    width: 9%;
    line-height: 1.5;
    }

    #rate, #pitch {
    float: right;
    width: 80%;
    }
    #rate{
        outline: none; /*去掉点击时出现的外边框*/
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none; /*这三个是去掉那条线原有的默认样式，划重点！！*/
        height: 3px;
        margin-top:8px;
        background:#7e57c2;
    }
    #pitch{
        outline: none; /*去掉点击时出现的外边框*/
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none; /*这三个是去掉那条线原有的默认样式，划重点！！*/
        height: 3px;
        margin-top:8px;
        background:#7e57c2;
    }
    input[type="range"]::-webkit-slider-thumb {
        /*::-webkit-slider-thumb是代表给滑块的样式进行变更*/
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none; /*//这三个是去掉滑块原有的默认样式，划重点！！*/
        -webkit-box-shadow:0 0 2px ; /*设置滑块的阴影*/
        width: 20px;
        height:20px;
        // background-color: #7e57c2;
        border-radius: 10px;
        // background-color: #7e57c2;
        // background: url("images/js2-d_03.png");
        // background-size: cover;
        /*//这几个是设置滑块的样式*/
        background-color:#fff;
        border:4px solid #7e57c2;
        }
    .controls {
    text-align: center;
    margin-top: 10px;
    }

    .controls button {
    padding: 10px;
    }
    
</style>