<template>
    <div class="seller">
        <div class="scroll_wrap" ref="ratingsScroll">
            <div class="cont">
                <div class="seller_detail module"> 
                    <div class="collect" :class="{'checked':collection}" @click="toggleCollect">
                        <div class="iconfont icon-collection"></div>
                        <div class="txt">{{collection?'已收藏':'收藏'}}</div>
                    </div>
                    <div class="desc">
                        <div class="title">{{seller.name}}</div>
                        <div class="sales">
                            <star :size="36" :score="seller.score"></star>
                            <span class="text">（{{seller.ratingCount}}）</span>
                            <span class="text">月售{{seller.sellCount}}单</span>
                        </div>
                    </div>
                    <ul class="remark">
                        <li class="block">
                            <div class="tit">起送价</div>
                            <div class="content">
                                <span class="price">{{seller.minPrice}}</span>元
                            </div>
                        </li>
                        <li class="block">
                            <div class="tit">商家配送</div>
                            <div class="content">
                                <span class="price">{{seller.deliveryPrice}}</span>元
                            </div>
                        </li>
                        <li class="block">
                            <div class="tit">平均配送时间</div>
                            <div class="content">
                                <span class="price">{{seller.deliveryTime}}</span>分钟
                            </div>
                        </li>
                    </ul>
                </div>
                <div class="distance"></div>
                <div class="seller_intro module">
                    <dl class="bulletin">
                        <dt class="title">公告与活动</dt>
                        <dd class="text">{{seller.bulletin}}</dd>
                    </dl>
                    <ul v-if="seller.supports"  class="supports_list">
                        <li class="item" v-for="item in seller.supports" :key="item.type">
                            <span class="icon" :style="{backgroundImage:`url(/static/images/list_icon0${item.type}.png)`}"></span>
                            <span class="txt" >{{item.description}}</span>
                        </li>
                    </ul>
                </div>
                <div class="distance"></div>
                <div class="seller_pics module" v-if="seller.pics">
                    <div class="title">商家实景</div>
                    <div class="pic_wrap" ref="picScroll">
                        <ul class="pic_list clearFix" ref="picList">
                            <li class="item" v-for="(pic,i) in seller.pics" :key="i"><img :src="pic" alt=""></li>
                        </ul>
                    </div>
                </div>
                <div class="distance"></div>
                <div class="seller_infos module">
                    <div class="title">商家信息</div>
                    <ul class="info_list">
                        <li class="item" v-for="(info,i) in seller.infos" :key="i">{{info}}</li>
                    </ul>
                </div>
            </div>
        </div>
        <shopcart :delivery-price="seller.deliveryPrice"  ref="shopcart"
                    :min-price="seller.minPrice"></shopcart>
    </div>    
</template>
<script type='text/ecmasctipt-6'>
import star from "components/star/star.vue";
import BScroll from "better-scroll";
import shopcart from "components/shopcart/shopcart.vue";

export default {
    components:{
        star,
        shopcart
    },
    props:{
        seller:Object
    },
    data(){
        return{
            collection:false
        }
    },
    created(){
        this.$nextTick(() => {
            this._initScroll();  //初始化滚动插件
            this._initPicScroll();
        });
    },
    methods:{
        _initScroll(){
            this.ratingsScroll = new BScroll(this.$refs.ratingsScroll,{
                click:true
            });
        },
        _initPicScroll(){
            if(this.seller.pics){
                let width = 120;
                let margin = 6;
                let piclen = this.seller.pics.length;
                this.$refs.picList.style.width = (width+margin) * piclen + 12+ 'px'
                this.picScroll = new BScroll(this.$refs.picScroll,{
                    scrollX: true
                })
            }
        },
        toggleCollect(){
            this.collection = !this.collection;
        }
    }
};
</script>
<style>
.distance{width: 100%;height: 16px;border:1px solid rgba(7,17,27,.1);border-width:1px 0;background:#f3f5f7;}
.seller{position: absolute;width: 100%;top: 174px;bottom:48px;overflow: hidden;}
.seller .scroll_wrap{height:100%;}
.seller .module{position: relative;padding:0 18px;}
.seller .module .title{font-size:14px;font-weight: 500;;line-height:14px;color:rgb(7, 17, 27);margin-bottom:8px;}
.seller_detail .collect{position: absolute;right:18px;top:22px;text-align: center;font-size:10px;color:rgb(77,85,93)}
.seller_detail .collect .iconfont{font-size:24px;line-height:24px;margin-bottom:4px;color:rgba(7, 17, 27,.2);}
.seller_detail .collect.checked .iconfont{color:rgb(240, 20, 20);}
.seller_detail .desc{padding:18px 0;}
.seller_detail .desc .sales .text{font-size:10px;color:rgb(77,85,93);line-height:18px;; }
.seller_detail .desc .star{display: inline-block;}
.seller_detail .desc .star .star_item{margin-right:8px;}
.seller_detail .remark{display: flex;align-items: center;border-top: 1px solid rgba(7,17,27,.1);padding:18px 0;}
.seller_detail .remark .block{flex:1;text-align: center;font-size:10px;color:rgb(147,153,159);}
.seller_detail .remark .block .content{margin-top: 4px;color:rgb(7,17,27);line-height: 24px;font-weight: 200;}
.seller_detail .remark .block .content .price{font-size: 24px;}
.seller_intro .bulletin{padding-top:18px;padding-bottom: 16px;}
.seller_intro .bulletin .text{padding:0 12px;margin-top:8px;font-size:12px;font-weight: 200;color:rgb(240,20,20);line-height: 24px;}
.seller_intro .supports_list .item,.seller_infos .info_list .item
{line-height:48px;font-size: 12px;color:rgb(7, 17, 27);font-weight: 200;border-top: 1px solid rgb(7,17,27,.1);}
.seller_intro .supports_list .item .icon{display: inline-block;vertical-align: middle;font-size: 0;width: 16px;height: 16px;background-repeat: no-repeat;
    background-size: 16px;margin-right: 6px;}
.module.seller_pics{padding:18px 0 18px 18px;}
.seller_pics .pic_list{margin-top: 12px;width: 500%;}
.seller_pics .pic_list .item{float: left;margin-right:6px;width: 120px;height: 90px;}
.seller_pics .pic_list .item img{width: 100%;height:100%;}
.module.seller_infos{padding-top:18px;}
.seller_infos .info_list{margin-top: 12px;}
</style>

