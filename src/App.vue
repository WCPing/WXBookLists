
<script>
import { get, showSuccess } from './util'
import qcloud from 'wafer2-client-sdk'
import config from './config'
export default {
    created() {
        // const res = await get('/weapp/demo')
        // console.log(res)
        console.log('start')
    }
}
</script>
<style>
.text-footer{
    text-align: center;
    font-size: 12px;
    margin-bottom: 5px;
}
.text-primary {
    color: #4DA2FF;
}
.right{
    float: right;
}
.page-title {
    padding-left: 20px;
    background: #eee;
    line-height: 40px;
    font-size: 14px;
}
.btn{
    color: #ffffff;
    background: #4DA2FF;
    margin-bottom: 10px;
    padding-left: 15px;
    border-radius: 16px;
    height:  40px;
    line-height: 40px;
    width: 100%; 
}
.btn:active {
    background-color: blue;
}
</style>


