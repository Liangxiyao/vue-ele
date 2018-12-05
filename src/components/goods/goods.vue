<template>
  <div class="goods">
    <div class="menu_wrapper" ref="menu">
      <ul class="menu_list">
        <li v-for="(item,index) in goods" :key="item.name" class="menu_item"
            :class="{'current':currentIndex == index}" @click="clickMenu(index,$event)">
          <span class="txt">{{item.name}}</span>
        </li>
      </ul>
    </div>
    <div class="foods_wrapper" ref="foods">
      <ul class="food_list_wrapper">
        <li v-for="item in goods" :key="item.name" class="food_list food_list_hook">
          <h1 class="title">{{item.name}}</h1>
          <ul class="item_wrapper">
            <li v-for="food in item.foods" :key="food.name" class="food_item" @click="foodDetail(food,$event)">
              <div class="img"><img width="100%" height="100%" :src="food.icon" alt=""></div>
              <div class="content">
                <h2 class="name ofellipsis">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now_price"><em>￥</em>{{food.price}}</span>
                  <s v-if="food.oldPrice"><em>￥</em>{{food.oldPrice}}</s>
                  <cart-control :food="food" class="fr" @cart-add="cartAdd" @cart-cut="cartCut"></cart-control>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :delivery-price="seller.deliveryPrice" ref="shopcart"
              :min-price="seller.minPrice"
              :select-foods="selectFoodsData">
    </shopcart>
    <food-detail :food="selectedfood" ref="shopDetail" @cart-add="cartAdd"></food-detail>
  </div>
</template>
<script>
  import BScroll from "better-scroll";
  import shopcart from "components/shopcart/shopcart.vue";
  import cartControl from "components/cartControl/cartControl.vue";
  import foodDetail from "components/foodDetail/foodDetail.vue";
  import state from "@/state.js";

  export default {
    data() {
      return {
        goods: [],
        listHeight: [],
        menuScrollY: 0,
        foodsScrollY: 0,
        selectedfood: {},
        state,
        dd: [],
        selectFoodsData: JSON.parse(localStorage.getItem('cartData')) || []
      };
    },
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      shopcart,
      cartControl,
      foodDetail,
    },
    created() {
      this.$http.get("/api/goods")
        .then(res => {
          var json = res.data;
          if (json.errno === 0) {
            this.goods = json.data;
            this.$nextTick(() => {
              this.initScroll();  //初始化滚动插件
              this.computeHeight();//计算屏幕高度
              let foods = JSON.parse(localStorage.getItem('cartData')) || []
              if (foods && foods.length > 0) {
//                this.goods.forEach((good) => {
//                  good.foods.forEach((food) => {
//                    for (let i = 0; i < foods.length; i++) {
//                      if (foods[i].count > 0 && foods[i].name == food.name) {
//                        this.$set(food, "count", foods[i].count)
//                        break
//                      }
//                    }
//                  })
//                });
                let isBreak = false
                for(let j=0;j<this.goods.length;j++){
                  this.goods[j].foods.forEach((food) => {
                    for (let i = 0; i < foods.length; i++) {
                      if (foods[i].count > 0 && foods[i].name == food.name) {
                        this.$set(food, "count", foods[i].count)
                        isBreak = true
                      }
                    }
                  })
                  if(isBreak){
                    break
                  }
                }
              }

            });
          }
        }).catch(err => {
        console.log(err);
      });
    },
    computed: {
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          //判断当前滚动的foodsScrollY 位置 ,如果在i 和 i+1  之间，那么当前是滚动在第i 个模块
          //最后一个区间没有height2
          if (!height2 || (this.foodsScrollY >= height1 && this.foodsScrollY < height2)) {
            return i;
          }
        }
        return 0;
      },
    },
    methods: {
      initScroll() {  //初始化滚动事件
        this.menuScroll = new BScroll(this.$refs.menu, {
          click: true, //默认关闭浏览器原生的点击事件，设置true ，将派发一个click事件
          probeType: 3
        });
        this.foodsScroll = new BScroll(this.$refs.foods, {
          click: true,
          probeType: 3 //状态3为实时派发scroll事件
        });
        this.menuScroll.on("scroll", pos => {
          this.menuScrollY = Math.abs(Math.round(pos.y));
        });
        this.foodsScroll.on("scroll", pos => {
          this.foodsScrollY = Math.abs(Math.round(pos.y));
        });
      },
      selectFoods() {  //购物车里的商品
        let foods = this.selectFoodsData
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count > 0) {
              let isHave = false;
              for (let i = 0; i < foods.length; i++) {
                if (foods[i].name == food.name) {
                  foods[i].count = food.count
                  isHave = true
                  break
                }
              }
              if (!isHave) {
                foods.push(food);
              }
            }
          })
        });
        this.selectFoodsData = foods
        localStorage.setItem('cartData', JSON.stringify(foods));
      },
      computeHeight() {
        let foodList = this.$refs.foods.getElementsByClassName("food_list_hook");
        let height = 0;
        this.listHeight.push(height);

        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }
      },
      clickMenu(index, event) {
        let foodList = this.$refs.foods.getElementsByClassName("food_list_hook");
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 30); //http://ustbhuangyi.github.io/better-scroll/doc/api.html
      },
      cartAdd(el) {
        this.$nextTick(() => {
          this.$refs.shopcart.drophandle(el);
          this.selectFoods()

        })
      },
      cartCut() {
        this.selectFoods()
      },
      foodDetail(food, event) {
        this.selectedfood = food;
        this.$refs.shopDetail.show();
      }
    }
  };
</script>
<style>
  .goods {
    display: flex;
    position: absolute;
    width: 100%;
    top: 174px;
    bottom: 48px;
    overflow: hidden;
  }

  .goods .menu_wrapper {
    flex: 0 0 80px;
    width: 80px;
    background: #f3f5f7;
    overflow: hidden;
  }

  .goods .menu_item {
    display: table;
    height: 54px;
    width: 56px;
    line-height: 14px;
    padding: 0 12px;
  }

  .goods .menu_item.current {
    background: #fff;
    position: relative;
    top: -1px;
    border-bottom: 0;
  }

  .goods .menu_item .txt {
    display: table-cell;
    vertical-align: middle;
    font-size: 12px;
    color: #333;
    border-bottom: 1px solid rgba(7, 17, 27, 0.1)
  }

  .goods .menu_item.current .txt {
    border-bottom: 0;
    font-weight: 700;
  }

  .foods_wrapper {
    flex: 1;
  }

  .foods_wrapper .food_list .title {
    padding-left: 14px;
    height: 26px;
    line-height: 26px;
    border-left: 2px solid #d9dde1;
    font-size: 12px;
    color: rgb(147, 153, 159);
    background: #f3f5f7;
  }

  .foods_wrapper .item_wrapper {
    padding: 0 18px;
  }

  .foods_wrapper .food_item {
    display: flex;
    padding: 18px 0;
    border-bottom: 1px solid rgba(7, 17, 27, 0.1);
  }

  .foods_wrapper .food_item:last-of-type {
    border-bottom: none;
  }

  .foods_wrapper .food_item .img {
    flex: 0 0 57px;
    height: 57px;
    margin-right: 10px;
  }

  .foods_wrapper .food_item .content {
    flex: 1;
    width: 0;
  }

  .foods_wrapper .food_item .content .name {
    font-size: 14px;
    color: rgb(7, 17, 27);
    line-height: 14px;
    margin: 2px 0 8px;
  }

  .foods_wrapper .food_item .content .desc,
  .foods_wrapper .food_item .content .price,
  .foods_wrapper .food_item .content .extra {
    font-size: 10px;
    color: rgb(147, 153, 159);
    line-height: 12px;
    margin-bottom: 5px;
  }

  .foods_wrapper .food_item .content .extra .count {
    margin-right: 12px;
  }

  .foods_wrapper .food_item .content .price {
    font-weight: 700;
    line-height: 24px;
    margin-bottom: 0;
  }

  .foods_wrapper .food_item .content .now_price {
    color: rgb(240, 20, 20);
    font-size: 14px;
    margin-right: 8px;
  }

  .foods_wrapper .food_item .content .price em {
    font-weight: normal;
    font-size: 10px;
  }

</style>
