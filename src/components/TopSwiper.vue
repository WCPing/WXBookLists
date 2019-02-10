<template>
    <div class="swiper">
        <swiper
          :indicator-dots='true'
          indicator-color='#4DA2FF'
          :autoplay='true'
          :interval='5000'
          :duration='600'
          :circular='true'>
         <div v-for="(top, imgindex) in imgUrls" :key="imgindex">
             <swiper-item>
                 <img 
                   class="slide-image" 
                   mode="aspectFit"
                   v-for="img in top"
                   :key='img.id'
                   :src="img.image"
                   @click='bookDetail(img)'>
             </swiper-item>
         </div>
        </swiper>
    </div>
</template>
<script>
export default {
    props: ['tops'],
    computed: {
        imgUrls() {
            let res = this.tops
            return [res.slice(0, 2), res.slice(2, 4), res.slice(4, 6)]
        }   
    },
    methods: {
        bookDetail(item) {
            wx.navigateTo({
                url: '/pages/Detail/main?id=' + item.id
            })
        }
    }
}
</script>
<style lang="scss" scoped>
.swiper{
    margin-top: 5px;
    .slide-image{
        width: 50%;
        height: 250rpx;
    }
}
</style>

