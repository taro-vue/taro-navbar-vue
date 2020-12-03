<template>
  <view class="nav-bar-box">
    <view
      :style="{
        paddingTop: style.top + 'px',
        paddingBottom: style.gap + 'px',
        textAlign: 'center',
        height: style.height + 'px',
        lineHeight: style.height + 'px',
        background: background,
      }"
    >
      <slot />
    </view>
    <view>
      <scroll-view scroll-y="true" :style="{ height: style.contentHeight }">
        <slot name="content" />
      </scroll-view>
    </view>
  </view>
</template>

<script>
import Taro from "@tarojs/taro";

export default {
  props: {
    background: {
      type: String,
      default: "#fff",
    },
    ifLoading: {
      type: Boolean,
      default: false,
    },
  },

  data() {
    return {
      style: {
        top: 0,
        height: 0,
        width: 0,
        gap: 0,
        contentHeight: 0,
      },
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      let systemInfo = wx.getSystemInfoSync();
      console.log(systemInfo.screenHeight);
      let gap = "";
      let rect = null;
      try {
        rect = Taro.getMenuButtonBoundingClientRect
          ? Taro.getMenuButtonBoundingClientRect()
          : null;
        if (rect === null) {
          throw "getMenuButtonBoundingClientRect error";
        }
        //取值为0的情况
        if (!rect.width) {
          throw "getMenuButtonBoundingClientRect error";
        }
        gap = rect.top - systemInfo.statusBarHeight;
      } catch (error) {
        //胶囊按钮上下间距 使导航内容居中
        let width = 96; //胶囊的宽度，android大部分96，ios为88
        if (systemInfo.platform === "android") {
          gap = 8;
          width = 96;
        } else if (systemInfo.platform === "devtools") {
          if (systemInfo.system.includes("ios")) {
            gap = 5.5; //开发工具中ios手机
          } else {
            gap = 7.5; //开发工具中android和其他手机
          }
        } else {
          gap = 4;
          width = 88;
        }
        if (!systemInfo.statusBarHeight) {
          //开启wifi的情况下修复statusBarHeight值获取不到
          systemInfo.statusBarHeight =
            systemInfo.screenHeight - systemInfo.windowHeight - 20;
        }
        rect = {
          //获取不到胶囊信息就自定义重置一个
          bottom: systemInfo.statusBarHeight + gap + 32,
          height: 32,
          left: systemInfo.windowWidth - width - 10,
          right: systemInfo.windowWidth - 10,
          top: systemInfo.statusBarHeight + gap,
          width: width,
        };
      }
      this.style = {
        ...rect,
        gap,
        contentHeight: systemInfo.screenHeight - rect.bottom - gap + "px",
      };
    },
    //处理上拉加载事件
    handlescrolltolower() {
      console.log(this.onscrolltolower);
      this.$emit("onscrolltolower");
    },
    handlescrolltoupper() {
      console.log("下拉刷新");
      this.$emit("onscrolltoupper");
    },
  },
};
</script>
<style>
.nav-bar-box {
  height: 100vh;
  overflow: hidden;
}
</style>
