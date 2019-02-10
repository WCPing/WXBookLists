<template>
    <div class="rate">
        <span>☆☆☆☆☆</span>
        <div class="hollow" :style="wstyle">
            ★★★★★
        </div>
        
    </div>
</template>
<script>
export default {
    props: {
        value: {
            type: [Number, String], 
            default: '0'
        }
    },
    computed: {
        wstyle() {
           return `width: ${this.value / 2}em`  //按理是除以2， 可是显示的似乎不正确
        }
    }

}
</script>
<style lang="scss" scoped>
.rate{
    position: relative;
    display: inline-block;
    .hollow{
        position: absolute;
        display: inline-block;
        top: 0;
        left: 0;
        width: 0;
        overflow: hidden;
    }
}
</style>
