<template>
  <div id="app">
    <v-header :seller="seller" ></v-header>
    <div class="tab">
      <router-link class="tab-item" to="/goods">商品</router-link>
      <router-link class="tab-item" to="/ratings">评价</router-link>
      <router-link class="tab-item" to="/seller">商家</router-link>
    </div>
    <keep-alive><router-view :seller="seller"/></keep-alive>
  </div>
</template>
<script>
import header from "./components/header/header";

export default {
  name: "App",
  data() {
    return {
      seller: {},
      selectFood:[]
    };
  },
  created() {
    this.$http.get("/api/seller")
              .then(res => {
                var json = res.data;
                if (json.errno === 0) {
                  this.seller = json.data;
                } else {
                }
              })
              .catch(err => {
                console.log(err);        
              });
  },
  components: {
    "v-header": header
  },
  methods:{
  }
};
</script>
<style>
.tab {
  display: flex;
  width: 100%;
  line-height:40px;
  border-bottom: 1px solid #f2f2f2;
  position: relative;
  z-index: 10;
}
.tab .tab-item {
  flex: 1;
  text-align: center;
  font-size:14px;
  color: rgb(77,85,93)
}
.tab .tab-item.router-link-active{color: rgb(240,20,20)}
</style>
