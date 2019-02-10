<template>
    <div>
       <BookInfo :info='info'></BookInfo>
       <CommentList :comments='comments'></CommentList>
       <div class="comment" v-if="showAdd">
           <textarea v-model='comment'
                 class="textarea" 
                 :maxlength='100'
                 placeholder='请输入图书短评'></textarea>
           <div class="location">
               地理位置：
               <switch color='#4DA2FF' :checked='location' @change="getGeo"></switch>
               <span class="text-primary">{{location}}</span>
           </div>
           <div class="phone">
               手机型号：
               <switch color='#4DA2FF' :checked='phone' @change="getPhone"></switch>
               <span class="text-primary">{{phone}}</span>
           </div>
           <button class="btn" @click="addComment">
               评论
           </button>
       </div>
       <div v-else class="text-footer">
           未登录或已经评论过啦
       </div>
       <button class="btn" open-type='share'>转发给好友</button>
    </div>
</template>
<script>
import {get, post, showModal} from '@/util'
import BookInfo from '@/components/BookInfo'
import CommentList from '@/components/CommentList'
export default {
    data() {
        return{
            bookid: '',
            info: {},
            comment: '',
            location: '',
            phone: '',
            userinfo: {},
            comments:[]
        }
    },
    components: {
        BookInfo,
        CommentList
    },
    computed: {
        showAdd() {
            //未登录
            if (!this.userinfo.openId) {
                return false
            }
            //已评论， 评论里可以查到自己的openid
            if (this.comments.filter(v => v.openid === this.userinfo.openId).length) {
                return false
            }
            return true
        }
    },
    mounted() {
       this.bookid = this.$root.$mp.query.id 
       this.getDetail()
       this.getComments()
       const userinfo = wx.getStorageSync('userinfo')
       console.log(userinfo)
       if (userinfo) {
           this.userinfo = userinfo
       }
    },
    methods: {
        async getDetail() {
            const info = await get('/weapp/bookdetail', {id: this.bookid})
            this.info = info
            wx.setNavigationBarTitle({
                title: info.title
            })
        },
        async getComments() {
            const comments =  await get('/weapp/commentlist', {bookid: this.bookid})
            this.comments = comments.list || []
        },
        getGeo(e) {
            const key = '9d6e585a5f266c4594b4af5e03a78ee1'
            let url = 'https://restapi.amap.com/v3/geocode/regeo'
            if (e.target.value) {
                wx.getLocation({
                    success: geo => {
                        wx.request({
                            url,
                            data: {
                                location: `${geo.longitude},${geo.latitude}`,
                                output: 'json',
                                key
                            },
                            success: res => {
                                if (res.data.status === '1') {
                                  this.location = res.data.regeocode.addressComponent.province  
                                } else {
                                  this.location = '未知地点'
                                }
                                
                            }
                        })
                    }
                })
            } else {
                this.location = ''
            }
        },
        getPhone(e) {
            if (e.target.value) {
                const phoneInfo = wx.getSystemInfoSync()
                this.phone = phoneInfo.model
            } else {
                this.phone = ''
            }
        },
        async addComment() {
            if (!this.comment) {
                return
            }
            const data = {
                openid: this.userinfo.openId,
                bookid: this.bookid,
                comment: this.comment,
                phone: this.phone,
                location: this.location
            }
           try {
              await post('/weapp/addcomment', data)
              this.comment = ''
              this.getComments()
           } catch (e) {
               showModal('失败', e.msg)
           }
        }
    }
}
</script>
<style lang="scss" scoped>
.comment{
    margin-top: 10px;
    textarea{
        width: 730rpx;
        height: 200rpx;
        background: #eee;
        padding: 10px;
    }
    .location{
        margin-top: 10px;
        padding: 5px 10px;
    }
    .phone{
        margin-top: 10px;
        padding: 5px 10px;
    }
}
</style>


