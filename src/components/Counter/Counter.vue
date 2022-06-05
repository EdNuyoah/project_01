<template>
  <div
    class="number-container d-flex justify-content-center align-items-center"
  >
    <!-- 减 1 的按钮 -->
    <button type="button" class="btn btn-light btn-sm" @click="sub">-</button>
    <!-- 购买的数量 -->
    <span class="number-box">{{ num }}</span>
    <!-- 加 1 的按钮 -->
    <button type="button" class="btn btn-light btn-sm" @click="add">+</button>
  </div>
</template>

<script>
import bus from "@/components/eventBus.js";
export default {
  props: {
    //Goods把id传过来,将来通过EventBus方案，
    //把数量传递到App.vue组件的时候，需要通知App更新哪个组件的数量
    id: {
      require: true,
      type: Number,
    },
    //接收Goods传过来的count
    num: {
      default: 1,
      type: Number,
    },
  },
  methods: {
    add() {
      //点击按钮，数值加一
      //要发送给App的格式{id,value} id 数量
      const obj = { id: this.id, value: this.num + 1 };
      //通过EventBus把obj发送给App组件

      bus.$emit("share", obj);
    },
    sub() {
      if (this.num - 1 === 0) return;
      //点击按钮，数值减一
      //要发送给App的格式{id,value} id 数量
      const obj = { id: this.id, value: this.num - 1 };
      //通过EventBus把obj发送给App组件

      bus.$emit("share", obj);
    },
  },
};
</script>

<style lang="less" scoped>
.number-box {
  min-width: 30px;
  text-align: center;
  margin: 0 5px;
  font-size: 12px;
}

.btn-sm {
  width: 30px;
}
</style>
