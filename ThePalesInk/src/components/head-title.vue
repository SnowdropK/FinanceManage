<template>
    <h1 class="header-title" v-text="title"></h1>
</template>
<script>
    export default {
        name: 'header-title',
        props: ['title']
    }
</script>
<style lang="scss">
    @import "../assets/scss/define";
    .header-title{
        @extend %f18;
        @extend %fwn;
        padding: 8px;
        color: #fff;
        background-color:#7e57c2;
        text-align:center;
        margin-top:0px;
    }
</style>
