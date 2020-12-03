<template>
  <view class="index">
    <NavBarVue
      :if-loading="ifLoading"
      @onscrolltolower="onscrolltolower"
      @onscrolltoupper="onscrolltoupper"
    >
      <template v-slot>
        <view class="header">首页</view>
      </template>
      <template v-slot:content>
        <view>内容</view>
      </template>
    </NavBarVue>
  </view>
</template>

<script>
import "./index.css";

export default {
  data() {
    return {};
  },
};
</script>
