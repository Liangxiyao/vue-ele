<template>
<div class="ratings_cont">
    <div  class="scroll_wrap" ref="ratingsScroll">
        <div class="scroll">
            <div class="rating_header">
                <dl class="overall_score">
                    <dt class="score">{{seller.score}}</dt>
                    <dd class="title">综合评分</dd>
                    <dd class="tip">高于周边商家{{seller.rankRate}}%</dd>
                </dl>
                <ul class="score_list">
                    <li class="item">
                        <span class="txt">服务态度 </span>
                        <star :size="36" :score="seller.serviceScore"></star>
                        <span class="score">{{seller.serviceScore}}</span>
                    </li>
                    <li class="item">
                        <span class="txt">商品评分 </span>
                        <star :size="36" :score="seller.foodScore"></star>
                        <span class="score">{{seller.foodScore}}</span>
                    </li>
                    <li class="item">
                        <span class="txt">送达时间 </span>
                        <span class="time">{{seller.deliveryTime}}分钟</span>
                    </li>
                </ul>
            </div>
            <div class="distance"></div>
            <div class="ratings">
                <div class="rate_header">
                    <div class="rateType" >
                    <span class="type" v-if="rateTypes" v-for="(item,i) in rateTypes" :key="i"
                                            :class="{'nolike':i == 2, 'active':activeType == item.type}"
                                            @click="switchType(item.type)">
                                            {{item.name}}<em>{{item.count}}</em>
                        </span>
                    </div>
                    <div class="tip" @click="checkedCont" :class="{checked:checked}"><i class="iconfont icon-radio"></i>只看有内容的评价</div>
                </div>
                <div class="ratings_wrap">
                    <ul class="rating_list" v-if="ratings && ratings.length>0">
                        <li class="item" v-for="(rating,i) in ratings" :key="i" v-show="needShow(rating.rateType,rating.text)">
                            <img class="avatar" width="28px" height="28px" :src="rating.avatar"/>
                            <div class="time">{{toTime(rating.rateTime)}}</div>
                            <div class="desc">
                                <div class="user">
                                    <p class="name">{{rating.username}}</p>
                                    <star :size="24" :score="rating.score"></star>
                                    <span class="deliveryTime" v-if="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
                                </div>
                                <div class="text">{{rating.text}}</div>
                                <div class="likefoods" v-if="rating.recommend.length">
                                    <i class="iconfont fl" :class="{'icon-like':rating.rateType==0,'icon-nolike':rating.rateType==1}"></i>
                                    <ul class="wrap">
                                        <li class="food ofellipsis" v-for="(food,i) in rating.recommend" :key="i">{{food}}</li>
                                    </ul>
                                </div>
                            </div>
                        </li>
                    </ul>
                    <div  v-else class="noRating">暂无评价</div>
                </div>
            </div>
        </div>
    </div>
    <shopcart :delivery-price="seller.deliveryPrice"  ref="shopcart"
              :min-price="seller.minPrice"
              :select-foods="selectFoods"></shopcart>
</div>
</template>
<script type="text/ecmascript-6">
import BScroll from "better-scroll";
import shopcart from "components/shopcart/shopcart"
import star from "components/star/star.vue";
import {timestampToTime} from "@/utils/public.js";
import state from "@/state.js"


export default {
    components:{
        star,
        shopcart
    },
    props:{
        seller:Object,
    },
    data(){
        return{
            checked:false,
            ratings:[],
            activeType:2,
            selectFoods:JSON.parse(localStorage.getItem('cartData')) || []
        }
    },
    created(){
        this.$http.get("/api/ratings")
                  .then(res=>{
                      var json = res.data;
                      if(json.errno === 0){
                          this.ratings = json.data;
                          this.$nextTick(() => {
                            this._initScroll();  //初始化滚动插件
                          });
                      }
                    }).catch(err => {
                        console.log(err);
                    });
    },
    activated(){
        /* if(localStorage.getItem('cartData')){
            this.selectFoods = JSON.parse(localStorage.getItem('cartData'))

        }else{
            this.selectFoods = state.selectfoodData;
        } */
    },
    computed:{
        rateTypes(){
            if(this.ratings && this.ratings.length){
                var data = [
                    {
                        name:'全部',
                        count:this.ratings.length,
                        type:2
                    },{
                        name:'满意',
                        count:this.ratings.filter(data => data.rateType == 0).length,
                        type:0
                    },{
                        name:'不满意',
                        count:this.ratings.filter(data => data.rateType == 1).length,
                        type:1
                    }
                ]
            return data;
            }
        }
    },
    methods:{
        _initScroll(){
            this.ratingsScroll = new BScroll(this.$refs.ratingsScroll, {
                click: true, //默认关闭浏览器原生的点击事件，设置true ，将派发一个click事件
                probeType: 3,

            });
            console.log(this.ratingsScroll);

        },
        checkedCont(){
            this.checked = !this.checked;
        },
        toTime(value){
            let time = timestampToTime(value)
            return time;
        },
        checkedCont(){
            this.checked = !this.checked;
        },
        switchType(type){
            this.activeType = type;
        },
        needShow(type,text){
            if(this.checked && !text){
                return false;
            }
            if(this.activeType == 2){
                return true;
            }else{
                return type === this.activeType
            }
        }
    }
}
</script>
<style>
.distance{width: 100%;height: 16px;border:1px solid rgba(7,17,27,.1);border-width:1px 0;background:#f3f5f7;}
.scroll_wrap{height:100%;}
.ratings_cont{position: absolute;width: 100%;top: 174px;bottom: 48px;overflow: hidden;}
.rating_header{display: flex;padding:12px 0;align-items:center;border-bottom: 1px solid #f2f2f2;}
.rating_header .overall_score{width:35%;text-align: center;border-right:1px solid #f2f2f2;}
.rating_header .overall_score .score{font-size:24px;line-height: 28px;color:rgb(255,153,0);margin-bottom:6px;}
.rating_header .overall_score .title{font-size:12px;color:rgb(7,17,27);line-height: 12px;margin-bottom: 8px;}
.rating_header .overall_score .tip{font-size:10px;color:rgb(147,153,159);line-height: 10px;margin-bottom: 6px;}
.rating_header .score_list{flex:1;width:0;padding:0 12px;}
.rating_header .score_list .item{margin-bottom:6px;}
.rating_header .score_list .txt{font-size:12px;line-height: 18px;color:rgb(7,17,27);margin-right:12px;}
.rating_header .score_list .star{display: inline-block;margin-right:12px;}
.rating_header .score_list .star_item{margin-right:6px;}
.rating_header .score_list .score{line-height: 18px;font-size: 12px;color:rgb(255,153,0)}
.rating_header .score_list .time{line-height: 18px;font-size: 12px;color:rgb(147,153,159)}
.ratings_cont .distance{width: 100%;height: 16px;border:1px solid rgba(7,17,27,.1);border-width:1px 0;background:#f3f5f7;}
.ratings_cont .ratings .rate_header{padding:0 16px;}
.ratings_cont .ratings .rateType{margin-top:12px;margin-bottom:18px;;}
.ratings_cont .ratings .rateType .type{padding:8px 12px;margin-right:8px;color:rgb(77,85,93);background: rgb(0,160,220,.2);font-size:12px;border-radius:2px;}
.ratings_cont .ratings .rateType .type.nolike{color:rgb(77,85,93);background: rgb(77,85,93,.2)}
.ratings_cont .ratings .rateType .type.active{background: rgb(0,160,240);color: #fff;}
.ratings_cont .ratings .rateType .type.nolike.active{background: rgb(77,85,93)}
.ratings_cont .ratings .tip{font-size:12px;color:#B7BBBF;line-height: 24px;border-top: 1px solid rgba(7,17,27,.1);padding:12px 0;;}
.ratings_cont .ratings .tip .iconfont{font-size:18px;margin-right: 4px;position: relative;top:-2px;}
.ratings_cont .ratings .checked .iconfont{color:#00c850;}
.ratings_cont .rating_list{padding:0 18px;border-top: 1px solid #f2f2f2;}
.ratings_cont .rating_list .item{display: flex;padding:16px 0;position: relative;border-bottom: 1px solid rgb(7,17,27,.1);}
.ratings_cont .rating_list .item:last-of-type{border:none;}
.ratings_cont .rating_list .user{font-size:10px;color:rgb(147,153,159);line-height:12px;margin-bottom: 6px;}
.ratings_cont .rating_list .user .name{margin-bottom: 4px;}
.ratings_cont .rating_list .avatar{border-radius:50%;margin-right:12px;}
.ratings_cont .rating_list .star{display: inline-block;}
.ratings_cont .rating_list .star_item{margin-right:2px;}
.ratings_cont .rating_list .time{position: absolute;right:0;top:16px;font-size:10px;color:rgb(147,153,159);}
.ratings_cont .rating_list .text{line-height:18px;color: rgb(7, 17, 27);font-size:12px;}
.ratings_cont .rating_list .likefoods{margin-top: 8px;}
.ratings_cont .rating_list .likefoods .iconfont{float: left;;color:rgb(0,160,220);line-height:24px;position: relative;top:-3px;}
.ratings_cont .rating_list .likefoods .icon-nolike{color:rgb(147,153,159)}
.ratings_cont .rating_list .likefoods .wrap{padding-left:16px;}
.ratings_cont .rating_list .likefoods .food{float: left;max-width:60px;font-size:9px;color:rgb(147,153,159);margin-left:8px;padding:0 6px;
border: 1px solid #f2f2f2;margin-bottom: 6px;line-height: 16px;}

@media screen and (max-width: 374px) { /*当屏幕尺寸小于375px时*/
  .rating_header .score_list{padding:0 10px;}
  .rating_header .score_list .txt{margin-right: 6px;}
  .rating_header .score_list .star{margin-right:0;}
  .ratings_cont .score_list .star36 .star_item{width:16px;height:16px;background-size:16px;}
}
</style>


