<template>
    <div class="container-view login-wrap" id="cos_bc">
        <head-title :title="'修改密码'"></head-title>
        <ul class="input-warp484">
            <li class="input-item">
                <x-input type="password" placeholder="请输入旧密码" :min="6" :max="6" v-model="old_password_value">
                    <svg slot="label" class="input-icon">
                        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#password-icon"></use>
                    </svg>
                </x-input>
            </li>
            <li class="input-item">
                <x-input type="password" placeholder="请输入新密码" :min="6" :max="6" v-model="password_value">
                    <svg slot="label" class="input-icon">
                        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#password-icon"></use>
                    </svg>
                </x-input>
            </li>
            <li class="input-item">
                <x-input type="password" placeholder="请确认新密码" :min="6" :max="6" v-model="too_password_value">
                    <svg slot="label" class="input-icon">
                        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#password-icon"></use>
                    </svg>
                </x-input>
            </li>
        </ul>
        <i class="sure-btn" @click="subFun()" :class="{'sure-active-true': old_password_value && password_value && too_password_value == password_value}">确认</i>
        <mu-float-button id="jumpMM" icon="回" class="demo-float-button" href="#/"/>
    </div>
</template>
<script>
    import headTitle from '../../components/head-title.vue'
    import types from '../../store/mutation-types'
    import Util from '../../assets/lib/Util'
    import Tool from '../../assets/lib/Tool'
    import { XInput } from 'vux'
    export default {
        name: 'modify',
        data () {
            return {
                old_password_value: '',
                password_value: '',
                too_password_value: '',
                is_timer: false
            }
        },
        created () {
            this.setNavIndex();
        },
        methods: {
            /**验证输入*/
            checkInput () {
                if ( !this.old_password_value ) {
                    this.showMsg('请输入旧密码');
                    return true;
                }
                if ( !this.password_value ) {
                    this.showMsg('请输入新密码');
                    return true;
                }
                if ( this.password_value != this.too_password_value ) {
                    this.showMsg('两次新密码不一致');
                    return true;
                }
                return false;
            },
            subFun () {
                if(this.checkInput()) return;
                if(this.is_timer) return;
                this.is_timer = true;
                this.$vux.loading.show({text:'Loading'});
                var user_name = Tool.dataToSessionStorageOperate.achieve('user').user_name;
                Util.modifyPassword({
                    user_name: user_name,
                    old_password: this.old_password_value,
                    password_value: this.password_value,
                    too_password_value: this.too_password_value
                },(result) => {
                    setTimeout(() => {
                        this.$vux.loading.hide();
                        this.is_timer = false;
                        this.showMsg(result.msg);
                        if (result.status == 1) {
                            setTimeout(() => {
                                Tool.dataToSessionStorageOperate.clear();
                                this.$router.push('/login')
                            },1000)
                        }
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
            /**设置导航条按钮状态*/
            setNavIndex () {
                this.$store.commit(types.SET_NAV_INDEX,'1')
            }
        },
        components: {
            headTitle,
            XInput
        }
    }
</script>
<style lang="scss">
    #cos_bc .input-item .weui-input{
        color:black;
    }
    #cos_bc{
        background-image:url('../../../static/img/bc.jpg');
        background-size: cover;
        bottom:0px;
    }
    #jumpMM{
        position:absolute;
        top:70%;
        right:45%;
    }
    .input-warp484{
        background-color: white;
        width:88%;
        margin:27px auto;
        border-radius: 12px;
        padding:45px 10px;
        box-shadow: 0 0 0 1px hsla(0,0%,100%,.3) inset,0 .5em 1em rgba(10, 10, 10, 0.6);
        border:10px solid #ee4f4f;
    }
</style>
