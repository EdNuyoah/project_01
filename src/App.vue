<template>
  <div class="app-container">
    <h1>App 根组件</h1>

    <Header title="购物车案例"></Header>

    <!-- 循环渲染每一个商品信息 -->
    <Goods
      v-for="item in list"
      :key="item.id"
      :id="item.id"
      :title="item.goods_name"
      :pic="item.goods_img"
      :price="item.goods_price"
      :state="item.goods_state"
      :count="item.goods_count"
      @state-change="getNewState"
    ></Goods>
    <!-- Footer区域 -->
    <Footer
      :isfull="fullState"
      :amount="amt"
      :all="total"
      @fullChange="getFullState"
    ></Footer>
  </div>
</template>

<script>
//导入axios
import axios from "axios";
import bus from "@/components/eventBus.js";
//导入需要组件
import Header from "@/components//Header/Header.vue";
import Goods from "@/components/Goods/Goods.vue";
import Footer from "@/components/Footer/Footer.vue";
export default {
  data() {
    return {
      //用来存储购物车的列表数据，默认为空数组
      list: [],
    };
  },

  //定义计算属性
  computed: {
    //动态计算出全选的状态true还是false
    fullState() {
      return this.list.every((item) => item.goods_state);
    },
    //已勾选商品的总价
    amt() {
      //先filter过滤，再reduce累加
      return this.list
        .filter((item) => item.goods_state)
        .reduce(
          (total, item) => (total += item.goods_price * item.goods_count),
          0
        );
    },
    //已勾选的商品的总数
    total() {
      this.list
        .filter((item) => item.goods_state)
        .reduce((t, item) => (t += item.goods_count), 0);
    },
  },
  created() {
    //调用请求数据的方法
    this.initCartList();
    bus.$on("share", (val) => {
      this.list.some((item) => {
        if (item.id === val.id) {
          item.goods_count = val.value;
          return true;
        }
      });
    });
  },
  methods: {
    //封装请求列表数据的方法
    async initCartList() {
      //调用axios的get方法，请求列表数据  请求回来在页面渲染期间要用到的数据，必须转存到data中
      const { data: res } = await axios.get("https://www.escook.cn/api/cart");
      if (res.status === 200) {
        this.list = res.list;
      }
    },
    //接收子组件传过来的复选框的id和选中状态
    getNewState(e) {
      this.list.some((item) => {
        if (item.id === e.id) {
          //把传过来的状态给
          item.goods_state = e.value;
          //阻止循环
          return true;
        }
      });
    },
    getFullState(e) {
      //接收Footer子组件传递过来的全选按钮的状态
      this.list.forEach((item) => (item.goods_state = e));
    },
  },

  components: {
    Header,
    Goods,
    Footer,
  },
};
</script>


<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
