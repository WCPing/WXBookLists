<template>
    <div class="container">
        <button class="unloginbtn" open-type="getUserInfo" lang="zh_CN" v-if="isLogined" @getuserinfo="created">登  录</button>
        <div class="userinfo">
            <img :src="userinfo.avatarUrl" alt="" >
            <p>{{userinfo.nickName}}</p>
        </div>

        <YearProgress></YearProgress>

        <button class="btn" @click="scanBook">添加图书</button>
    </div>
</template>
<script>
import YearProgress from '@/components/YearProgress'
import { get, showSuccess, post, showModal } from '../../util'
import qcloud from 'wafer2-client-sdk'
import config from '../../config'
export default {
    data() {
        return {
            userinfo: {},
            isLogined: true
        }
    },
    components: {
        YearProgress
    },
    onPullDownRefresh() {
        this.getUserInfo()
    },
    async created() {
        this.getUserInfo()
        let user = wx.getStorageSync('userinfo')
        // if (!user) {
        //     let that = this
        //     qcloud.setLoginUrl(config.loginUrl);
        //     qcloud.login({
        //         success: function (userInfo) {
        //             console.log('登录成功', userInfo)
        //             showSuccess('登录成功')
        //             wx.setStorageSync('userinfo', userInfo)
        //             that.getUserInfo()
        //         },
        //         fail: function (err) {
        //             console.log('登录失败', err);
        //         }
        //     });
        // }
        if (user) {
            // 第二次登录
            // 或者本地已经有登录态
            // 可使用本函数更新登录态
            qcloud.loginWithCode({
            success: res => {
                showSuccess('登录成功')
                wx.setStorageSync('userinfo', res)
                that.getUserInfo()
            },
            fail: err => {
                console.error(err)
                showModal('登录失败', '请检查网络连接状态')
            }
            })
        } else {
            // 首次登录
            qcloud.setLoginUrl('https://xox0irwv.qcloud.la/login');
            qcloud.login({
            success: res => {
                showSuccess('登录成功')
                wx.setStorageSync('userinfo', res)
                that.getUserInfo()
            },
            fail: err => {
                console.error(err)
                // showModal('登录错误', '请检查网络连接状态')
            }
            })
        }
    },
    methods: {
        getUserInfo() {
            this.userinfo = wx.getStorageSync('userinfo')
            this.isLogined = !this.userinfo ? true : false
        },
        async addBook(isbn) {
            const res = await post('/weapp/addbook', {
                isbn,
                openid: this.userinfo.openId
            })
            showModal('添加成功', `《${res.title}》添加成功`)
        },
        scanBook() {
            wx.scanCode({
                success: (res) => {
                   if (res.result) {
                       this.addBook(res.result)
                       console.log(res)
                   }
                }
            })
        }
    }
}
</script>
<style>
 .container{
     padding: 0 30rpx;
 }
 .userinfo{
     margin-top:  100rpx;
     text-align: center;
 }
 .userinfo img {
     width:  150rpx;
     height:  150rpx;
     margin: 20rpx;
     border-radius: 50%;
 }
 .unloginbtn{
     width:  150rpx;
     height:  150rpx;
     border-radius: 50%; 
     border:none;
     font-size:18px;
     padding:0;
     text-align:center;
     line-height:150rpx;
     color: #ffffff;
     background-color: #4DA2FF;
     margin-top:100rpx;
    margin-bottom:-100rpx;
 }

</style>


