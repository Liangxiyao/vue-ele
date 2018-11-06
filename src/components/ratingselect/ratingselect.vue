<template>
    <div class="ratings">
      <div class="rate_header">
          <slot name="title"></slot>
          <div class="rateType" >
              <span class="type"  v-for="(item,i) in typeDesc" :key="i" 
                                  :class="{nolike:item.type==1,active:item.type==rateType}">{{item.name}}
                                  <span class="count"></span>
              </span>
          </div>
          <div class="tip" @click="checkedCont" :class="{checked:checked}"><i class="iconfont icon-radio"></i>只看有内容的评价</div>
      </div>
      <div class="ratings_wrap"> 
          <ul class="rating_list" v-if="ratings && ratings.length>0">
              <li class="rating" v-for="(rating,i) in ratings" :key="i">
                  <div class="user">
                      <span class="name">{{rating.username}}</span>
                      <img class="avatar" width="12px" height="12px" :src="rating.avatar"/>
                  </div>
                  <div class="time">{{toTime(rating.rateTime)}}</div>
                  <div class="text">
                      <i class="iconfont" :class="{'icon-like':rating.rateType==0,'icon-nolike':rating.rateType==1}"></i>
                      {{rating.text}}
                  </div>
              </li>
          </ul>
          <div  v-else class="noRating">暂无评价</div>
      </div>
    </div>
</template>
<script>
import { timestampToTime } from "@/utils/public.js";

const LIKE = 0;
const NOLIKE = 1;
const ALL = 2;
export default {
  props: {
    ratings: {
      type: Array,
      default() {
        return [];
      }
    },
    rateType: {
      type: Number,
      default: ALL
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    typeDesc: {
      type: Array,
      default() {
        return [
          {
            type: 2,
            name: "全部"
          },
          {
            type: 0,
            name: "满意"
          },
          {
            type: 1,
            name: "不满意"
          }
        ];
      }
    }
  },
  data() {
    return {
      checked: this.onlyContent
    };
  },
  methods: {
    checkedCont() {
      this.checked = !this.checked;
    },
    toTime(value) {
      let time = timestampToTime(value);
      return time;
    }
  }
};
</script>

<style>
.ratings .rate_header{padding:0 16px;}
.ratings .rateType{margin-top:12px;margin-bottom:18px;;}
.ratings .rateType .type{padding:8px 12px;margin-right:8px;background: rgb(0,160,220,.2);color:rgb(77,85,93);font-size:12px;border-radius:2px;}
.ratings .rateType .type .count{font-size:8px;}
.ratings .rateType .type.nolike{background: rgb(77,85,93,.2)}
.ratings .rateType .type.active{color:#fff;background: rgb(0,160,240)}
.ratings .rateType .type.nolike.active{color:#fff;background: rgb(77,85,93)}
.ratings .tip{font-size:12px;color:#B7BBBF;line-height: 24px;border-top: 1px solid rgba(7,17,27,.1);padding:12px 0;;}
.ratings .tip .iconfont{font-size:18px;margin-right: 4px;position: relative;top:-2px;}
.ratings .checked .iconfont{color:#00c850;}
.rating_list{padding:0 18px;border-top: 1px solid #f2f2f2;}
.rating_list .rating{padding:16px 0;position: relative;border-bottom: 1px solid rgb(7,17,27,.1);}
.rating_list .rating:last-of-type{border:none;}
.rating_list .user{position: absolute;right:0;top:10px;font-size:10px;color:rgb(147,153,159);line-height:24px;}
.rating_list .avatar{border-radius:50%;margin-left:6px;vertical-align: middle;;}
.rating_list .time{font-size:10px;color:rgb(147,153,159);line-height:12px;margin-bottom:6px;}
.rating_list .text{line-height:16px;color: rgb(7, 17, 27);font-size:12px;}
.rating_list .text .iconfont{color:rgb(0,160,220);line-height:24px;position: relative;top:-2px;}
.rating_list .text .icon-nolike{color:rgb(147,153,159)}
</style>

