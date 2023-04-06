<template>
  <view>
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <!-- <swiper-item v-for="(item,i) in swiperList" :key="i">
        <view class="swiper-item">
          <image :src="item.image_src"></image>
        </view>
      </swiper-item> -->
      <swiper-item v-for="(item,i) in swiperList" :key="i">
      <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
        <image :src="item.image_src"></image>
      </navigator>
      </swiper-item>
     
    </swiper>
   
    <view class="nav-list">
      <view class="nav-item" v-for="(item,i) in navlist" :key="i" @click="navClickHandler(item)">
        <image :src="item.image_src" class="nav-img"></image>
      </view>
    </view>
    
    <view class="floor-list">
      <view class="floor-item" v-for="(item , i) in floorList" :key="i">
        <image :src="item.floor_title.image_src" class="floor-title"></image>
    
    <!-- 楼层图片区    -->
    <view class="floor-img-box">
      <!-- 左侧大图片&&右侧小图片 -->
      <navigator class="left-img-box" :url="item.product_list[0].url">
      <view class="left-img-box">
        <image :src="item.product_list[0].image_src" 
        mode="widthFix"
        :style="{width:item.product_list[0].image_width + 'rpx'}"></image>
      </view>
      </navigator>
      
      <view class="right-img-box">
        
        <navigator class="right-img-item" v-for="(item2,i2) in item.product_list" :key="i2" v-if="i2 !==0" :url="item2.url">
          <image :src="item2.image_src" :style="{width : item2.image_width+'rpx'}" mode="widthFix"></image>
        </navigator>
      </view>
    </view>
    
    
        
      </view>
    </view>
    
  </view>
</template>

<script>
  import { $http } from '@escook/request-miniprogram'
  export default {
    data() {
      return {
        //轮播图数据列表
        swiperList:[],
        navlist:[],
        floorList:[]
      };
    },
    onLoad() {
      this.getSwiperList(),
      this.getNaviList(),
      this.getFloorList()
    },
    
    methods:{
      async getSwiperList(){
       const {data:res} =  await uni.$http.get('/api/public/v1/home/swiperdata')

       if(res.meta.status !== 200){
         return uni.$showMsg()
       }
       else{
         this.swiperList = res.message
         uni.$showMsg('数据请求成功')
       }
      },
      
      
      async getNaviList(){
        const {data:res} =  await  uni.$http.get('/api/public/v1/home/catitems')
        if(res.meta.status !== 200) return uni.$showMsg()
        this.navlist = res.message
      },
      
      navClickHandler(item){
        if(item.name==="分类"){
          uni.switchTab({
            url:'/pages/cate/cate'
          })
        }
      },
      
      async getFloorList(){
        const {data:res} = await uni.$http.get('/api/public/v1/home/floordata')
        if(res.meta.status !== 200) return uni.$showMsg()
        
   
   
       res.message.forEach(floor => {
         floor.product_list.forEach(prod =>{
           prod.url = '/subpkg/goods_list/goods_list?'+ prod.navigator_url.split('?')[1]
         })
       })  
        
        this.floorList = res.message
      },
      
      
    }
  }
</script>

<style lang="scss">
  
  swiper{
    height: 330rpx;
    .swiper-item,
    image{
      width: 100%;
      height: 100%;
    }
  }
  
  .nav-list{
    display: flex;
    justify-content: space-around;
    margin: 15px 0;
    
    .nav-img {
      width: 128rpx;
      height: 140rpx;
    }
  }
  
  .floor-title{
    height: 60rpx;
    width: 100%;
    display: flex;
  }
  
  .floor-img-box{
    display: flex;
  }
  
  .right-img-box{
    display: flex;
    flex-wrap: wrap;
  }

</style>
