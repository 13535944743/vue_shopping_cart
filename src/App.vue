<template>
  <div class="app-container container">
    <Header></Header>
    <Goods v-for="(item) in list" 
    :key="item.id" 
    :name="item.goods_name" 
    :state="item.goods_state" 
    :imgSrc="item.goods_img" 
    :price="item.goods_price" 
    :id="item.id"
    :count="item.goods_count"
    @state-change="getNewState"></Goods>
    <Footer :fullState="fullState"
    :allPrice="fullPrice"
    :allCount="fullCount"
     @allStateChange="getNewFullState"></Footer>
  </div>
</template>

<script>
import Header from '@/components/Header/Header.vue'
import Goods from '@/components/Goods/Goods.vue'
import Footer from '@/components/Footer/Footer.vue'

import bus from '@/components/EventBus.js'

import axios from 'axios'

export default {
  data() {
    return {
      list: [],
      test: 1
    }
  },
  methods: {
    async initCarList() {
      const {data: result} = await axios.get('https://www.escook.cn/api/cart');
      if (result.status === 200) {
        this.list = result.list;
      }
    },
    getNewState(e) {
      this.list.some(item => {
        if(item.id == e.id) {
          item.goods_state = e.value;
          return true;
        }
      })
    },
    getNewFullState(e) {
      this.list.forEach(element => {
        element.goods_state = e;
      });
    }
  },
  computed: {
    fullState() {
      return this.list.every(item => item.goods_state);
    },
    fullPrice() {
      return this.list.filter(item => item.goods_state).reduce((amount, item) => amount +=  item.goods_price * item.goods_count, 0);
    },
    fullCount() {
      return this.list.filter(item => item.goods_state).reduce((fincount, item) => fincount += item.goods_count, 0);
    }
  },
  components: {
    Header,
    Goods,
    Footer,
  },
  created() {
    this.initCarList();
    bus.$on('shareNewCount', (e) => {
      this.list.some(item => {
        if(item.id === e.id) {
          item.goods_count = e.count;
          return true;
        }
      });
    })
  }
}
</script>

<style lang="less">
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}

.container {
  padding-left: 0 !important;
  padding-right: 0 !important;
}
</style>
