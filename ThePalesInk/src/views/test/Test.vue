<template>
    <div class="container-view">
    </div>
</template>
<script>
    export default {
        name: 'account',
        computed: {
            animateName () {
                return this.$store.state.animate_name;
            }
        }
    }
</script>
<style lang="scss">
    @import "../../assets/scss/define";
  
</style>
