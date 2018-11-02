<template>
 <transition name="slideRight">
    <div class="foodDetail" v-show="showFlag" ref="detailScroll">
        <div class="food_cont">
            <div class="back" @click="goBack"><i class="iconfont icon-arrow"></i></div>
            <div class="header_img">
                <img :src="food.image" alt="">
            </div>
            <div class="content pr">
                <div class="title">{{food.name}}</div>
                <div class="sell_status">
                    <span class="count">月售{{food.sellCount}}份</span>
                    <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                    <span class="now_price"><em>￥</em>{{food.price}}</span>
                    <s class="old_price" v-if="food.oldPrice"><em>￥</em>{{food.oldPrice}}</s>
                </div>
                <div class="cartControl_wrap">
                    <transition name="hide" >
                        <div class="addcartBtn" v-show="!food.count" @click="addcartBtn">加入购物车</div>
                    </transition>
                </div>
                <cart-control :food="food"></cart-control>
            </div>
            <div class="distance"></div>
            <div class="intro">
                <div class="title">商品介绍</div>
                <div class="txt">{{food.info?food.info:"暂无商品介绍"}}</div>
            </div>
            <div class="distance"></div>
            <div class="ratings">
                <div class="rate_header">
                    <div class="title">商品评价</div>
                    <div class="rateType" >
                        <span class="type" v-if="rateTypes" v-for="(item,i) in rateTypes" :key="i" 
                                           :class="{like:item.type==1,nolike:item.type==2}">
                                           {{item.name}}<em>{{item.count}}</em>
                        </span>
                    </div>
                    <div class="tip" @click="checkedCont" :class="{checked:checked}"><i class="iconfont icon-radio"></i>只看有内容的评价</div>
                </div>
                <div class="ratings_wrap"> 
                    <ul class="rating_list" v-if="food.ratings && food.ratings.length>0">
                        <li class="rating" v-for="(rating,i) in food.ratings" :key="i">
                            <div class="user">
                                <span class="name">{{rating.username}}</span>
                                <img class="avatar" width="12px" height="12px" :src="rating.avatar"/>
                            </div>
                            <div class="time">{{rating.rateTime}}</div>
                            <div class="text">
                                <i class="iconfont" :class="{'icon-like':rating.rateType==0,'icon-nolike':rating.rateType==1}"></i>
                                {{rating.text}}
                            </div>
                        </li>
                    </ul>
                    <div  v-else class="noRating">暂无评价</div>
                </div>
            </div>
        </div>
    </div>
</transition>
</template>
<script>
import BScroll from "better-scroll";
import cartControl from "components/cartControl/cartControl.vue"

export default {
    components:{
        cartControl
    },
    props:{
        food:Object
    },
    data(){
        return{
            showFlag:false,
            checked:false,
        }
    },
    computed:{
        rateTypes(){ 
            if(this.food && this.food.ratings){  
                var data = [
                    {
                        name:'全部',
                        count:this.food.ratings.length,
                        type:0
                    },{
                        name:'推荐',
                        count:this.food.ratings.filter(data => data.rateType == 0).length,
                        type:1
                    },{
                        name:'吐槽',
                        count:this.food.ratings.filter(data => data.rateType == 1).length,
                        type:2
                    }
                ]  
            return data;  
            }
        },
    },
    methods:{
        show(){
            if(!this.food){
                return;
            }else{
                this.showFlag = true;  
                this.detailScroll = new BScroll(this.$refs.detailScroll,{
                    click:true
                }); 
            }   
        },
        goBack(){   
            this.showFlag = false;
        },
        addcartBtn(event){
            this.$emit('cart-add',event.target);
            this.$set(this.food,'count',1) 
        },
        checkedCont(){
            this.checked = !this.checked;     
        }
    }
}
</script>
<style>
.foodDetail{position: fixed;top:0;bottom:48px;z-index:30;width: 100%;background: #fff;transition:all .4s;}
.slideRight-enter,.slideRight-leave-to{transform:translateX(100%);opacity:0;}
.foodDetail .header_img{position: relative;width: 100%;height:0;padding-top:100%;}
.foodDetail .header_img img{position: absolute;top: 0;left: 0;width:100%;height: 100%;}
.foodDetail .back{position: absolute;top:10px;left:0;z-index:10;}
.foodDetail .back .iconfont{padding:10px;font-size:20px;color:#fff;}
.foodDetail .content{padding: 18px;}
.foodDetail .content .title{font-size: 14px;font-weight: 700;line-height: 14px;color:rgb(7, 17, 27);margin-bottom: 8px;}
.foodDetail .sell_status{font-size:10px;color:rgb(147,153,159);line-height: 20px;}
.foodDetail .sell_status .count{margin-right:12px;}
.foodDetail .price{font-size:14px;line-height: 24px;font-weight:700;color:rgb(240,20,20)}
.foodDetail .price em{font-weight: normal;}
.foodDetail .price .now_price em{font-size:10px;}
.foodDetail .price .old_price{font-size:10px;color:rgb(147,153,159)}
.foodDetail .cartControl_wrap{position: absolute;right: 18px;bottom: 18px;z-index: 3;}
.foodDetail .addcartBtn{padding:6px 12px;border-radius:20px;background:rgb(0,160,220);color:#fff;font-size:14px;}
.foodDetail .cartControl{position: absolute;right: 18px;bottom:18px;z-index: 2;}
.foodDetail .distance{width: 100%;height: 16px;border:1px solid rgba(7,17,27,.1);border-width:1px 0;background:#f3f5f7;}
.foodDetail .intro{padding: 18px;}
.foodDetail .intro .title,.foodDetail .rate_header .title{font-size:14px;font-weight:600;line-height: 14px;margin-bottom: 6px; }
.foodDetail .intro .txt{font-size: 12px;line-height: 24px;color:rgb(77, 85, 93);}
.foodDetail .ratings .rate_header{padding:16px 16px 0;}
.foodDetail .ratings .rateType{margin-top:12px;margin-bottom:18px;;}
.foodDetail .ratings .rateType .type{padding:8px 12px;margin-right:8px;background: rgb(0,160,240);color: #fff;font-size:12px;border-radius:2px;}
.foodDetail .ratings .rateType .type.like{color:rgb(77,85,93);background: rgb(0,160,220,.2)}
.foodDetail .ratings .rateType .type.nolike{color:rgb(77,85,93);background: rgb(77,85,93,.2)}
.foodDetail .ratings .tip{font-size:12px;color:rgb(147,153,159);line-height: 24px;border-top: 1px solid rgba(7,17,27,.1);padding:12px 0;;}
.foodDetail .ratings .tip .iconfont{font-size:18px;margin-right: 4px;position: relative;top:-2px;}
.foodDetail .ratings .checked .iconfont{color:#00c850;}
.foodDetail .rating_list{padding:0 18px;border-top: 1px solid rgba(7,17,27,.2);}
.foodDetail .rating_list .rating{padding:16px 0;position: relative;border-bottom: 1px solid rgb(7,17,27,.1);}
.foodDetail .rating_list .rating:last-of-type{border:none;}
.foodDetail .rating_list .user{position: absolute;right:0;top:16px;font-size:10px;color:rgb(147,153,159);line-height:24px;}
.foodDetail .rating_list .avatar{border-radius:50%;margin-left:6px;vertical-align: middle;;}
.foodDetail .rating_list .time{font-size:10px;color:rgb(147,153,159);line-height:12px;margin-bottom:6px;}
.foodDetail .rating_list .text{line-height:16px;color: rgb(7, 17, 27);font-size:12px;}
.foodDetail .rating_list .text .iconfont{color:rgb(0,160,220);line-height:24px;position: relative;top:-2px;}
.foodDetail .rating_list .text .icon-nolike{color:rgb(147,153,159)}
</style>
