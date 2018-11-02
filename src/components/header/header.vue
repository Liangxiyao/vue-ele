<template>
    <div class="header">
        <div class="cont_wrapper">
            <div class="avatar">
                <img width="64" height="64" :src="seller.avatar" alt="">
            </div>
            <div class="context">
                <div class="tit">
                    <span class="brand"></span>
                    <span class="name">{{seller.name}}</span>
                </div>
                <div class="describe">{{seller.description}}/{{seller.deliveryTime}}分钟送达</div>
                <div v-if="seller.supports" class="support">
                    <span class="icon"></span>
                    <span class="txt">{{seller.supports[0].description}}</span>
                </div>
            </div>
        </div>
        <div @click="showDetail" class="bulletin ofellipsis">{{seller.bulletin}}</div>
        <div class="bg_img">
            <img :src="seller.avatar" alt="" width="100%" height="100%">
        </div>
        <transition name="fade"> 
            <div v-show="detailShow" class="detail">
                <div class="detail_wrapper clearFix">
                    <div class="detail_cont">
                        <h6 class="name">{{seller.name}}</h6>
                        <div class="star_wrapper">
                            <star :size="48" :score="seller.score"></star>
                        </div>
                        <div class="title">
                            <div class="line"></div>
                            <div class="txt">优惠信息</div>
                            <div class="line"></div>
                        </div>
                        <ul v-if="seller.supports"  class="supports_list">
                            <li v-for="item in seller.supports" :key="item.type">
                                <span class="icon" :style="{backgroundImage:`url(/static/images/list_icon${item.type}.png)`}"></span>
                                <span class="txt" >{{item.description}}</span>
                            </li>
                        </ul>
                        <div class="title">
                            <div class="line"></div>
                            <div class="txt">优惠信息</div>
                            <div class="line"></div>
                        </div>
                        <p class="info">{{seller.bulletin}}</p>
                    </div>
                </div>
                <div @click="closeDetail" class="detail_close">x</div>
            </div>
        </transition>
    </div>
</template>

<script type="text/ecmascript-6">
import star from '../star/star.vue'

export default {
    props:{
        seller:{
            type:Object,
        }
    },
    data(){
        return{
            detailShow:false,
        }
    },
    components:{
        star:star
    },
    methods:{
        showDetail(){
            this.detailShow = true
        },
        closeDetail(){
            this.detailShow = false
        }
    }
}
</script>
<style>
.header{background-color:rgba(7, 17, 27, 0.5);color:#fff;overflow: hidden;position: relative;}
.header .cont_wrapper{padding:24px 12px 18px 24px;font-size:0;}
.header .avatar{display: inline-block;vertical-align: middle}
.header .avatar img{border-radius:4px;}
.header .context{display:inline-block;font-size:12px;margin-left:16px;}
.header .context .tit{font-size:16px;font-weight:600;line-height:18px;margin-bottom:5px;}
.header .describe{font-size:12px;margin-bottom:5px;}
.header .support .txt{font-size:10px;font-weight: 200;}
.header .bulletin{height: 28px;line-height: 28px;padding:0 12px;background:rgba(7, 17, 27, 0.2);font-size:10px;font-weight: 200;}
.header .bg_img{width:100%;height:100%;position: absolute;top: 0;left: 0;z-index:-1;filter: blur(10px)}
.header .detail{position: fixed;top:0;left:0;z-index: 100;width: 100%;height: 100%;overflow: auto;background-color:rgba(7, 17, 27, .8);
backdrop-filter:blur(10px);/*模糊*/transition: all .6s ease;}
.fade-enter,.fade-leave-to{opacity: 0;}
.header .detail_wrapper{min-height:100%;width: 100%;  }
.header .detail_wrapper .detail_cont{margin-top:64px;padding-bottom:64px;}
.header .detail_wrapper .detail_cont .name{text-align: center;margin-bottom: 16px;font-size: 16px;font-weight: 700;}
.header .detail_close{position: relative;width: 32px;height: 32px; margin: -64px auto 0;font-size: 32px;}
.header .detail_cont .title{display: flex;width: 80%;margin:30px auto;}
.header .detail_cont .title .line{flex:1;border-bottom: 1px solid rgba(255,255,255,0.2);position: relative;top:-9px;}
.header .detail_cont .title .txt{font-size:14px;font-weight:700;padding:0 12px; }

.header .supports_list{width:80%;margin:0 auto 6px;font-size: 12px;font-weight: 200;}
.header .supports_list li{line-height:26px;padding-left:12px;}
.header .supports_list .icon{display: inline-block;font-size: 0;width: 16px;height: 16px;background-repeat: no-repeat;background-size:16px;margin-right:6px;}
.header .detail_wrapper .info{width: 80%;margin: 0 auto;font-size:12px;font-weight: 200;line-height: 24px;padding:0 12px;box-sizing:border-box;}


</style>


