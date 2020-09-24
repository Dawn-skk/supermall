<template>
<div id="home">
  <nav-bar class="home-nav"><template v-slot:center>购物街</template></nav-bar>
  <scroll class="conter">
    <home-swiper :banners = "banners"></home-swiper>
    <recommend-view :recommends = "recommends"/>
    <feature-view />
    <tab-control class="tab-control" 
                  :titles="['流行','新款','精选']" 
                  @tabClick="tabClick"/>
    <goods-list :goods = "showGoods" />
  </scroll>
</div>
</template>

<script>
  import HomeSwiper from "./childComps/HomeSwiper"
  import RecommendView from "./childComps/RecommendView"
  import FeatureView from "./childComps/FeatureView"

  import NavBar from 'components/common/navbar/NavBar';
  import TabControl from 'components/content/tabControl/TabControl';
  import GoodsList from 'components/content/goods/GoodList';
  import Scroll from "components/common/scroll/Scroll"

  import {getHomeMultidata,getHomeGoods} from "network/home"

  export default {
    name: "Home",
    components:{
      NavBar,
      HomeSwiper,
      RecommendView,
      FeatureView,
      TabControl,
      GoodsList,
      Scroll
    },
    data() {
      return {
        banners: [],
        recommends: [],
        goods:{
          "pop":{page:0, list:[]},
          "new":{page:0, list:[]},
          "sell":{page:0, list:[]},
        },
        currentType:"pop"
      }
    },
    created(){
      // 1.请求多个数据
      this.getHomeMultidata()
      //请求商品数据
      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')
    },
    computed:{
      showGoods(){
        return this.goods[this.currentType].list
      }
    },
    methods:{
    /**
     * 事件监听的方法
     */
      tabClick(index){
        switch(index){
          case 0:
            this.currentType = 'pop'
          break;
          case 1:
            this.currentType = "new"
          break;
          case 2:
            this.currentType = "sell"
          break;
        }
      },
    /** 
      * 网络请求相关方法
      */
      getHomeMultidata(){
        getHomeMultidata().then((res)=>{
          this.banners = res.data.banner.list
          this.recommends = res.data.recommend.list
        })
      },
      getHomeGoods(type){
        const page = this.goods[type].page + 1 
        getHomeGoods(type,page).then(res => {
          this.goods[type].list.push(...res.data.list) 
          this.goods[type].page += 1
        })
      }
    }
  }
</script>

<style scoped>
  #home  {
    padding-top: 44px;
  }
  .home-nav {
    background-color: var(--color-tint);
    color: #FFF;
    position: fixed;
    top: 0;
    right: 0;
    left: 0;
    z-index: 10;
  }
  .tab-control {
    position: sticky;
    top: 44px;
  }
  .conter{
    height: calc(100% - 98px);
  }
</style>
