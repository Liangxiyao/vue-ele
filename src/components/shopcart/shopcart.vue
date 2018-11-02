<template>
<div>
  <div class="shopcart">
    <div class="cont">
        <div class="l_cont" @click="toggleList">
            <div class="logo_wrap fl pr" :class="{hasfoods:totalCount>0}">
                <div class="logo fl pr" >
                    <i class="iconfont icon-cart"></i>
                    <span class="total_count" v-show="totalCount>0">{{totalCount}}</span>
                </div>
                <div class="price fl">￥{{totalPrice}}</div>
            </div>
            <div class="desc fl">另需要配送费￥{{deliveryPrice}}</div>
        </div>
        <div class="r_cont pay" :class="[totalPrice >= minPrice ? 'enough':''] " @click="toPay"> {{payDecs}} </div>
        <div class="ball_container">
            <transition name="drop" tag="div" 
                        v-for="(ball,i) in balls" :key="i" 
                        @before-enter="beforeEnter"
                        @enter="enter"
                        @after-enter="afterEnter">
                <div  class="ball" v-show="ball.show">
                    <div class="inner inner-hook"></div>
                </div>
            </transition>
        </div>
    </div>
    <transition name="slideUp">
        <div class="shopcart_list" v-show="listShow">
          <div class="list_header clear">
            <h5 class="title fl">购物车</h5>
            <span class="empty fr" @click="emptyCart">清空</span>
          </div>
          <div class="list_wrap" ref="cartListWrap">
            <ul class="lists">
              <li class="food cleaxFix" v-for="(food,i) in selectFoods" :key="i">
                <span class="name">{{food.name}}</span>
                <div class="countControl_wrap fr">
                  <cart-control :food="food"></cart-control>
                </div>
                <span class="price fr"> <em>￥</em>{{food.price * food.count}}</span>
              </li>
            </ul>
          </div>
        </div>
    </transition>
  </div>
  <transition name="fadeMask">
    <div class="mask" v-show="listShow" @click="listHide"></div>
  </transition>
</div>
</template>

<script>
import cartControl from "components/cartControl/cartControl.vue";
import BScroll from "better-scroll"

export default {
  props: {
    selectFoods: {
      type: Array,
      default() {
        return [];
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      balls: [
        //定义抛物线小球的状态,定义多个是因为动画完成需要时间，如果此时多次触发，则会导致没有小球可用
        { show: false },
        { show: false },
        { show: false },
        { show: false },
        { show: false }
      ],
      dropBalls: [],
      open: false //是否展开购物车
    };
  },
  components: {
    cartControl
  },
  computed: {
    totalPrice() {
      let total = 0;
      this.selectFoods.forEach(food => {
        total += food.price * food.count;
      });
      return total;
    },
    totalCount() {
      let count = 0;
      this.selectFoods.forEach(food => {
        count += food.count;
      });
      return count;
    },
    payDecs() {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`;
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice;
        return `还差￥${diff}元起送`;
      } else {
        return `去结算`;
      }
    },
    listShow() {
      if (!this.totalCount) {
        return;
      }
      let show = this.open;
      if(show){ //当购物车列表展开的时候
        this.$nextTick(()=>{
          if(!this.cartListScroll){ //如果滚动事件不存在，new BScroll
            this.cartListScroll = new BScroll(this.$refs.cartListWrap,{
              click:true,
              probeType: 3
            })
          }else{
            this.cartListScroll.refresh(); //调用BScroll方法刷新
          }        
        })
      }
      return show;
    }
  },
  methods: {
    toggleList() {  //购物车列表展开
      if (!this.totalCount) {
        return;
      }
      this.open = !this.open;
    },
    listHide(){ //点击遮罩层隐藏购物车列表
      this.open = false;
    },
    emptyCart(){  //清空购物车
      this.selectFoods.forEach((food)=>{
        food.count = 0;
      })
    },
    toPay(){
      if(this.totalPrice < this.minPrice){
        return;
      }
      alert('去支付')
    },
    drophandle(el) {
      //此方法在父组件goods.vue调用了
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i];
        console.log(ball);
        if (!ball.show) {
          ball.show = true;
          ball.el = el;
          this.dropBalls.push(ball);//将false的小球放到dropBalls
          return; //此处return终结函数,这意味着每次触发事件,dropBalls内只增加一个ball
        }
      }
    },
    //drop动画
    beforeEnter(el) {
      //这里的el指的是小球的Dom,与drop(el)参数区分开
      let count = this.balls.length;
      while (count--) {
        let ball = this.balls[count];
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect(); //获取小球的相对于视口的位移(小球高度)
          let x = rect.left - 32;
          let y = -(window.innerHeight - rect.top - 22);

          el.style.display = "";
          el.style.webkitTransform = `translate3d(0,${y}px,0)`;
          el.style.transform = `translate3d(0,${y}px,0)`;
          //处理内层动画
          let inner = el.getElementsByClassName("inner-hook")[0]; //使用inner-hook类 来单纯标记此类名被js操作
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
          inner.style.transform = `translate3d(${x}px,0,0)`;
        }
      }
    },
    enter(el, done) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight; //此处是为了触发浏览器重绘
      this.$nextTick(() => {
        el.style.webkitTransform = "translate3d(0, 0, 0)";
        el.style.transform = "translate3d(0, 0, 0)";
        let inner = el.getElementsByClassName("inner-hook")[0];
        inner.style.webkitTransform = "translate3d(0, 0, 0)";
        inner.style.transform = "translate3d(0, 0, 0)";

        el.addEventListener("transitionend", done); // 为了知道过渡的完成，否则无法进入到afterEnter中
      });
    },
    afterEnter(el) {
      let ball = this.dropBalls.shift(); //完成一次动画就删除一个dropBalls的小球，否则触发N次事件，dropBalls则有N个元素
      if (ball) {
        ball.show = false;
        el.style.display = 'none'
      }
    }
  }
};
</script>
<style>
.shopcart{position: fixed;left: 0;bottom: 0;z-index:99;width: 100%;height: 48px;line-height: 48px;background:#000;}
.shopcart .cont{display: flex;background: #141D27;position: relative;z-index:2;}
.shopcart .cont .l_cont{flex:1;}
.shopcart .l_cont .logo_wrap:after{position: absolute;top:12px;right: 0;content:'';height:24px;width: 1px;background:rgba(255,255,255,0.1);}
.shopcart .l_cont .logo{width:44px;height:44px;line-height: 44px;text-align: center;background:#2B343C;border-radius:50%;margin: -8px 12px 0;
border:6px solid #141D27;z-index: 99;}
.shopcart .l_cont .logo .iconfont{font-size:24px;color:rgba(255,255,255,0.4)}
.shopcart .l_cont .logo .total_count{position: absolute;top:-5px;right:-8px;font-size:10px;color:#fff;background: #F01414;
padding:0 7px;line-height:16px;border-radius:8px;}
.shopcart .l_cont .logo_wrap .price{font-size:16px;font-weight: 700;color:rgba(255,255,255,0.4);margin-right:12px;}
.shopcart .l_cont .desc{margin-left:12px;font-size:12px;color:rgba(255,255,255,0.4);}
.shopcart .l_cont .hasfoods .logo{background:#00A0DC;}
.shopcart .l_cont .hasfoods .iconfont{color:#fff;}
.shopcart .l_cont .hasfoods .price{color:#fff;}
.shopcart .cont .pay{flex:0 0 105px;width: 105px;background:#2B333B;font-size: 12px;color:rgba(255,255,255,.4);font-weight: 700;text-align: center}
.shopcart .cont .pay.enough{background: #00b43c;color:#fff;}

.ball_container .ball{position: fixed;left:32px;bottom:22px;z-index:1;transition:all .6s cubic-bezier(0.49, -0.29, 0.75, 0.41);}
.ball_container .inner{width: 16px;height: 16px;border-radius:50%;background: rgba(0,160,220);transition:all .6s }

.shopcart_list{position: absolute;bottom: 48px;left: 0;width:100%;background: #fff;transition:all .5s;transform:none;z-index:1;}
.slideUp-enter,.slideUp-leave-to{transform: translateY(100%);opacity: 0;z-index: -1;}
.shopcart_list .list_header{height: 40px;line-height: 40px;padding:0 16px;background: #f3f5f7;border-bottom: 1px solid rgba(7,17,27,0.1);}
.shopcart_list .list_header .title{font-size: 14px;color:rgb(7,17,27);font-weight: 200;}
.shopcart_list .list_header .empty{font-size: 12px;color:rgb(0,160,220);}
.shopcart_list .list_wrap{max-height:215px;overflow: hidden;}
.shopcart_list .lists{padding:0 18px 24px;}
.shopcart_list .lists .food{border-bottom: 1px solid rgba(7,17,27,.1);line-height:48px;}
.shopcart_list .lists .name{font-size: 14px;color:rgba(7,17,27);}
.shopcart_list .lists .price{font-size:14px;color:rgb(240,20,20);font-weight: 700;margin-right:12px;}
.shopcart_list .lists .price em{font-size:10px;}

.mask{position: fixed;top:0;left: 0;width:100%;height: 100%;z-index:50;background: rgba(7,17,27,0.6);opacity: 1;transition:all .5s;}
.fadeMask-enter,.fadeMask-leave-to{opacity: 0;background:rgba(7,17,27,0);}
</style>


