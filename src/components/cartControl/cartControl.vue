<template>
    <div class="cartControl clearFix">
        <transition name="move"> 
            <div class="l_wrapper fl" v-show="food.count>0">
                <span class="iconfont icon-cut fl" @click.stop="cutCount"></span>
                <div class="count fl" >{{food.count}}</div>
            </div>
        </transition>
        <div class="iconfont icon-add fr" @click.stop="addCount"></div>
    </div>
</template>

<script>
import state from '@/state.js';

export default {
    props:{
        food:{
            type:Object
        }
    },
    data(){
        return {
            nowfood:this.food,
            state,
        }
    },
    methods:{
        addCount(event){ 
            if(!this.food.count){
                this.$set(this.food,"count",1); //此处需要$set设置，否则不会触发视图层更新
            }else{
                this.food.count++;
            }
            this.$emit('cart-add',event.target);
        },
        cutCount(){
            if(this.food.count){
                this.food.count --;          
            }
        }
    }
}
</script>
<style>
.cartControl .l_wrapper{transition:all .3s ease;}
.cartControl .move-enter,.cartControl .move-leave-to{transform:translateX(24px);opacity:0;}
.cartControl .iconfont{color:#00A0DC;font-size:24px;line-height:24px;padding:2px;}
.cartControl .count{width: 22px;line-height: 24px;color:rgb(147,153,159);font-size:10px;text-align: center;padding:2px;}
</style>
