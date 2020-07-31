<template>
  <div class="select">
    <van-notice-bar scrollable :text="`当前选择——${this.Selected}`" />
    <div class="CurrentRestaurant">
      <van-image class="imgdiv" width="12rem" height="12rem" fit="contain" :src="Current.src" />
      <div class="RestaurantN">
        {{Current.name}}
        <van-tag class="tip" plain type="primary">餐厅</van-tag>
      </div>
    </div>
    <div class="countDown">
      <van-count-down
        ref="countDown"
        millisecond
        :time="`${Time*1000}`"
        :auto-start="false"
        format="ss:SS"
        @finish="finish"
      >
        <template v-slot="timeData">
          <span class="block">{{ timeData.minutes }}</span>
          <span class="colon">分</span>
          <span class="block">{{ timeData.seconds }}</span>
          <span class="colon">秒</span>
          <span class="block">{{ timeData.milliseconds}}</span>
          <span class="colon">毫秒</span>
        </template>
      </van-count-down>
      <div class="time">
        当前选择:
        <van-stepper class="timeSelect" theme="round" button-size="22" v-model="Time" />秒
      </div>
      <van-grid clickable>
        <van-grid-item text="开始" icon="play-circle-o" @click="start" />
        <van-grid-item text="暂停" icon="pause-circle-o" @click="pause" />
        <van-grid-item text="重置" icon="replay" @click="reset" />
      </van-grid>
    </div>
  </div>
</template>
<script>
import {
  NoticeBar,
  Divider,
  Cell,
  CellGroup,
  Button,
  Toast,
  Image,
  Lazyload,
  Tag,
  CountDown,
  Grid,
  GridItem,
  Stepper,
} from "vant";
import "vant/lib/index.css";
import axios from "axios";
export default {
  components: {
    [Stepper.name]: Stepper,
    [Grid.name]: Grid,
    [GridItem.name]: GridItem,
    [CountDown.name]: CountDown,
    [Button.name]: Button,
    [Divider.name]: Divider,
    [Cell.name]: Cell,
    [CellGroup.name]: CellGroup,
    [NoticeBar.name]: NoticeBar,
    [Toast.name]: Toast,
    [Image.name]: Image,
    [Lazyload.name]: Lazyload,
    [Tag.name]: Tag,
  },
  data() {
    return {
      curentimg: "../assets/haidilao.jpg",
      Selected: [],
      Current: {
        name: "请点击下方开始",
        src: "",
      },
      totalRestaurant: [],
      Time: 10,
      TimerStats: false,
    };
  },
  created() {
    if (this.$route.query.selected != null) {
      this.Selected = this.$route.query.selected;
    } else {
      Toast.fail("请先选择要选择的分类");
      setTimeout(() => {
        this.$router.push({ path: "/home.html" });
      }, 1000);
    }
    axios
      .get(
        "https://www.fastmock.site/mock/dc40487b929bef3c8a88ba701cc507af/vuedemo/api/getRest"
      )
      .then((res) => {
        console.log(this.Selected);
        let result = res.data;
        for (let i = 0; i < result.length; i++) {
          if (this.Selected.includes(result[i].class)) {
            console.log(this.Selected.includes(result[i].class));
            this.totalRestaurant.push(result[i]);
          }
        }
      });
  },
  methods: {
    start() {
      this.TimerStats = true;
      this.$refs.countDown.start();
      this.randomSelect();
    },
    pause() {
      this.TimerStats = false;
      clearInterval(this.Timer);
      this.$refs.countDown.pause();
    },
    reset() {
      this.TimerStats = false;
      clearInterval(this.Timer);
      this.$refs.countDown.reset();
    },
    finish() {
      clearInterval(this.Timer);
      Toast(`最终结果嘿嘿——${this.Current.name}`);
    },
    randomSelect() {
      let max = this.totalRestaurant.length;
      if (this.TimerStats == true) {
        this.Timer = setInterval(() => {
          let current = Math.floor(Math.random() * max);
          this.Current.name = this.totalRestaurant[current].name;
          this.Current.src = this.totalRestaurant[current].imgsrc;
        }, 100);
      }
    },
  },
};
</script>
<style>
.time {
  display: flex;
  justify-content: center;
  margin: 1rem 0rem;
  font-size: 0.8rem;
}
.timeSelect {
  margin: 0rem 0.5rem;
}
.select {
  height: 100vh;
  width: 100vw;
  overflow: hidden;
}
.countDown {
  position: absolute;
  bottom: 0px;
  width: 100%;
}
.van-grid-item {
  flex-basis: 33.3% !important;
}
.van-count-down {
  margin: 1rem 0rem;
  display: flex;
  justify-content: center;
}
.colon {
  display: inline-block;
  margin: 0 4px;
  line-height: 2rem;
  font-size: 0.8rem;
  color: #ee0a24;
}
.block {
  display: inline-block;
  width: 2rem;
  height: 2rem;
  line-height: 2rem;
  color: #fff;
  font-size: 1rem;
  text-align: center;
  background-color: #e94356fa;
  box-shadow: 0 2px 4px rgb(231, 163, 163);
}
.imgdiv {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
  display: block;
  margin: 2rem auto;
}
.RestaurantN {
  position: relative;
  text-align: center;
  font-size: 1rem;
  font-weight: 600;
}
.tip {
  position: absolute;
  top: -0.5rem;
}
</style>