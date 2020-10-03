<template>
    <div class="container-view login-wrap" id="login_bc">
        <head-title :title="'登录'" style="background-color:rgba(0,0,0,0);color:white;margin-top:65px;font-size:30px;"></head-title>
        <ul class="input-warp" style="width:80%;margin:23px auto 0px;">
            <li class="input-item">
                <x-input placeholder="请输入帐号" v-model="name_value" title="帐号：">
                    <svg slot="label" class="input-icon">
                        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#user-icon"></use>
                    </svg>
                </x-input>
                <!-- <mu-auto-complete filter="noFilter" label="配置 datasource 显示 icon" labelFloat :dataSource="dataSource"/> <br/> -->
            </li>
            <li class="input-item">
                <x-input type="password" placeholder="请输入密码" v-model="password_value" title="密码：">
                    <svg slot="label" class="input-icon">
                        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#password-icon"></use>
                    </svg>
                </x-input>
            </li>
        </ul>
        <div class="user-relevant" style="width:75%;margin:10px auto;">
            <span @click=" is_remember = !is_remember " :class="{ 'remember-active-user': is_remember }" class="remember-user">
                <i class="remember-type"></i>
                记住密码
            </span>
            <a href="#/retrieve" class="back-password">忘记密码？</a>
        </div>
        <i class="sure-btn" @click="login()" :class="{'sure-active-true':name_value && password_value}">登录</i>
        <a href="#/register" class="user-link">没有帐号？点我去注册</a>
    </div>
</template>
<script>
    import headTitle from '../../components/head-title.vue'
    import types from '../../store/mutation-types'
    import Util from '../../assets/lib/Util'
    import Tool from '../../assets/lib/Tool'
    import { XInput } from 'vux'
    export default {
        name: 'login',
        data () {
            return {
                is_remember: true,
                name_value: '',
                password_value: '',
                is_timer: false
            }
        },
        created () {
            this.$store.commit(types.JUDGE_IS_NOT_FIRST,false);
            this.fetchUserForLocalStorage();
        },
        mounted(){
            this.speedstar();
        },
        methods: {
            /**流星*/
            speedstar(){
                var stars = document.getElementById('login_bc')
                var star = document.getElementsByClassName('star')

                // js随机生成流星
                for (var j = 0; j < 70; j++) {
                    var newStar = document.createElement("div")
                    newStar.className = "star"
                    newStar.style.top = randomDistance(-500, -520) + 'px'
                    newStar.style.left = randomDistance(150, 20) + 'px'
                    stars.appendChild(newStar)
                }

                // 封装随机数方法
                function randomDistance(max, min) {
                    var distance = Math.floor(Math.random() * (max - min + 1) * 10 + min)
                    return distance
                }

                // 给流星添加动画延时
                for (var i = 0, len = star.length; i < len; i++) {
                    if (i % 6 == 0) {
                        star[i].style.animationDelay = '0s'
                    } else {
                        star[i].style.animationDelay = i * 0.4 + 's'
                    }
                }
            },
            /**获取本地用户信息*/
            fetchUserForLocalStorage () {
                var user = Tool.dataToLocalStorageOperate.achieve('rem-user');
                if (user) {
                    this.is_remember = true;
                    this.name_value = user.user_name;
                    this.password_value = user.user_password;
                } else {
                    this.is_remember = false;
                }
            },
            /**登录*/
            login () {
                if (this.checkInput()) return;
                if (this.is_timer) return;
                this.is_timer = true;
                var user = {
                    user_name: this.name_value,
                    user_password: this.password_value
                };
                this.$vux.loading.show({text:'Loading'});
                Util.login(user,(result) => {
                    setTimeout(() => {
                        this.$vux.loading.hide();
                        this.is_timer = false;
                        if (result.status == 1) {
                            /**帐号密码验证合法*/
                            var data = result.data;
                            Tool.dataToSessionStorageOperate.save('token',data.token);
                            Tool.dataToSessionStorageOperate.save('user',data.user);
                            this.rememberUser(user);
                            this.$store.commit(types.JUDGE_IS_NOT_FIRST,true);
                            this.$router.push('/');
                        } else {
                            this.showMsg(result.msg);
                        }
                    },100)
                })
            },
            checkInput () {
                if ( !this.name_value ) {
                    this.showMsg('请输入帐号');
                    return true;
                }
                if ( !this.password_value ) {
                    this.showMsg('请输入密码');
                    return true;
                }
                return false;
            },
            showMsg (msg) {
                this.$vux.toast.show({
                    type: 'text',
                    width: 'auto',
                    text: msg,
                    position: 'top'
                });
            },
            /**记住密码*/
            rememberUser (user) {
                if (this.is_remember) {
                    Tool.dataToLocalStorageOperate.save('rem-user',user);
                } else {
                    Tool.dataToLocalStorageOperate.remove('rem-user')
                }
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
    .input-item .weui-input{
        color:white;
    }
    #login_bc{
        background-image:url('../../../static/img/login_bg.jpg');
        background-size: cover;
        bottom:0px;
    }
    .user-link{
        @extend %c9;
        margin: 20px 140px;
    }
    .user-relevant{
        @extend %c9;
        @extend %pr;
        @extend %clearfix;
        @extend %f12;
        margin: 20px;
    }
    .remember-user{
        @extend %fl;
        @extend %cp;
        @extend %pr;
        padding-left: 16px;
        transition: color .5s;
        &.remember-active-user{
            color: #58B7FF;
            .remember-type{
                &:after{
                    background-color: #58B7FF;
                }
            }
        }
    }
    .remember-type{
        @extend %pa;
        @extend %t50;
        @extend %l0;
        margin-top: -6px;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        border: 1px solid #58B7FF;
        &:after{
            @extend %pa;
            @extend %t50;
            @extend %l50;
            margin-top: -3px;
            margin-left: -3px;
            width: 6px;
            height: 6px;
            border-radius: 50%;
            content: '';
            transition: background-color .5s;
            background-color: #fff;
        }
    }
    .back-password{
        @extend %fr;
        color: #58B7FF;
    }
    .login-wrap,.register-wrap{
        @extend %oya;
        .sure-btn.sure-active-true{
            background-color: #9370DB;
            box-shadow: 0 3px 0 0 rgb(103, 69, 172);
            border:0px solid  rgb(103, 69, 172);
        }
        .sure-btn{
            width:70%;
            margin: 30px auto;
            height: 34px;
            line-height: 34px;
            border-radius: 30px;
            background-color: rgba(0,0,0,0);
            border:2px solid #fff;
            box-shadow: 0 0 0 0 rgb(103, 69, 172);
        }
        .input-error{
            border-bottom: 1px solid #FF4949;
            &:after,
            &:before{
                background-color: #FF4949;
            }
            .input-icon{
                fill: #FF4949;
            }
            .weui-input{
                color: #FF4949;
            }
        }
    }
    .input-icon{
        @extend %vam;
        width: 40px;
        padding-right: 10px;
        height: 20px;
        fill: #fff;
        transition: fill .5s;
    }






    .star {
    display: block;
    width: 1px;
    background: transparent;
    position: relative;
    opacity: 0;
    /*过渡动画*/
    
    animation: star-fall 3s linear infinite;
    -webkit-animation: star-fall 3s linear infinite;
    -moz-animation: star-fall 3s linear infinite;
}
.star:after {
    content: '';
    display: block;
    border: 0px solid #fff;
    border-width: 0px 90px 2px 90px;
    border-color: transparent transparent transparent rgba(255, 255, 255, .5);
    box-shadow: 0 0 1px 0 rgba(255, 255, 255, .1);
    /*变形*/
    
    transform: rotate(-45deg) translate3d(1px, 3px, 0);
    -webkit-transform: rotate(-45deg) translate3d(1px, 3px, 0);
    -moz-transform: rotate(-45deg) translate3d(1px, 3px, 0);
    transform-origin: 0% 100%;
    -webkit-transform-origin: 0% 100%;
    -moz-transform-origin: 0% 100%;
}
@keyframes star-fall {
    0% {
        opacity: 0;
        transform: scale(0.5) translate3d(0, 0, 0);
        -webkit-transform: scale(0.5) translate3d(0, 0, 0);
        -moz-transform: scale(0.5) translate3d(0, 0, 0);
    }
    50% {
        opacity: 1;
        transform: translate3d(-200px, 200px, 0);
        -webkit-transform: translate3d(-200px, 200px, 0);
        -moz-transform: translate3d(-200px, 200px, 0);
    }
    100% {
        opacity: 0;
        transform: scale(1.2) translate3d(-300px, 300px, 0);
        -webkit-transform: scale(1.2) translate3d(-300px, 300px, 0);
        -moz-transform: scale(1.2) translate3d(-300px, 300px, 0);
    }
}
</style>
