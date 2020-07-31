<template>
  <div class="home">
    <van-notice-bar scrollable text="今晚吃啥听天由命" />
    <div class="title">随机选店小能手</div>
    <div class="group">
      <van-checkbox-group ref="checkboxGroup" v-model="result">
        <van-cell-group>
          <van-cell
            class="ceil"
            v-for="(item, index) in Classifylist"
            clickable
            :key="item"
            :title="`${item}`"
            @click="toggle(index)"
          >
            <template #right-icon>
              <van-checkbox :name="item" ref="checkboxes" />
            </template>
          </van-cell>
        </van-cell-group>
      </van-checkbox-group>
      <van-button class="btSelectAll" type="primary" @click="checkAll">{{AllSelect?'反选':'全选'}}</van-button>
    </div>
    <div class="bont">
      <van-button
        class="bt"
        color="linear-gradient(to right, #43e97b, #38f9d7)"
        @click="clickNext()"
      >选择完毕，进入下一步</van-button>
    </div>
  </div>
</template>
<script>
import {
  NoticeBar,
  Divider,
  Cell,
  CellGroup,
  Checkbox,
  CheckboxGroup,
  Button,
  Toast,
} from "vant";
import "vant/lib/index.css";
export default {
  components: {
    [Button.name]: Button,
    [Divider.name]: Divider,
    [Cell.name]: Cell,
    [CellGroup.name]: CellGroup,
    [Checkbox.name]: Checkbox,
    [CheckboxGroup.name]: CheckboxGroup,
    [NoticeBar.name]: NoticeBar,
    [Toast.name]: Toast,
  },
  data() {
    return {
      AllSelect: false,
      Classifylist: [
        "火锅",
        "烧烤",
        "炸鸡",
        "茶餐厅",
        "江浙菜",
        "自助餐",
        "西餐",
        "面食",
      ],
      result: [],
    };
  },
  methods: {
    toggle(index) {
      this.$refs.checkboxes[index].toggle();
    },
    clickNext() {
      if (this.result.length < 1) {
        Toast.fail("请先选择要选择的分类");
      } else {
        Toast.loading({
          message: "加载中...",
          forbidClick: true,
        });
        setTimeout(() => {
          Toast.clear();
          this.$router.push({
            path: "/select.html",
            query: { selected: this.result },
          });
        }, 800);
      }
    },
    checkAll() {
      console.log(this.AllSelect);
      if (this.AllSelect == true) {
        this.$refs.checkboxGroup.toggleAll();
      } else {
        this.$refs.checkboxGroup.toggleAll(true);
      }
      this.AllSelect = !this.AllSelect;
    },
  },
};
</script>
<style>
.ceil {
  font-size: 0.8rem;
}
.bt {
  width: 100%;
}
.bont {
  margin: 10%;
  text-align: center;
}
.group {
  display: flex;
  flex-direction: column;
}
.btSelectAll {
  width: 2.8rem;
  height: 1.5rem;
  font-size: 0.5rem;
  margin-top: 1rem;
  margin-left: auto;
  margin-right: 0.8rem;
}
.title {
  margin: 1rem;
  font-size: 1.5rem;
  font-weight: 600;
  text-align: center;
}
</style>