<template>
<div class="star" :class="starType">
    <span v-for="(item,i) in itemClasses"  class="star_item" :class = "item" :key="i"></span>
</div>    
</template>
<script>
const len = 5;
const cls_on = 'on';
const cls_half = 'half';
const cls_off = 'off';

export default {   
  props: {
    size: { type: Number },
    score: { type: Number }
  },
  data() { 
      return{}
  },
  computed: {
    starType() {
      return "star" + this.size;
    },
    itemClasses() {
      let result = [];
      let score = Math.floor(this.score * 2) / 2;
      let integer = Math.floor(score); //选中的整星个数
      let hasDecimal = score % 1 !== 0; //判断是否有0.5 半星

      for (let i = 0; i < integer; i++) {  //选中的整星个数
        result.push(cls_on);
      }
      if (hasDecimal) { //半星
        result.push(cls_half);
      }
      while (result.length < len) {  //未选中的星
        result.push(cls_off);
      }
      return result
    }
  }
}
</script>
<style>
.star{text-align: center;font-size:0;}
.star .star_item{display: inline-block;vertical-align: middle;margin-right:20px;background:url(./star48_off@2x.png) no-repeat 0 0 ;}
.star .star_item:last-of-type{margin-right: 0;}
.star .star_item.on {background-image:url(./star48_on@2x.png);}
.star .star_item.half{background-image:url(./star48_half@2x.png);}
.star48 .star_item{width:24px;height:24px;background-size:24px;}
.star36 .star_item{width:16px;height:16px;background-size:16px;}
.star24 .star_item{width:12px;height:12px;background-size:12px;}

</style>


