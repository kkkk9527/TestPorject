<template>
  <!--商标-->
  <div class="brand">
    <div class="py-container">
      <ul class="brand-list">
        <li class="brand-item">
          <img src="@/images/home/brand_21.png" />
        </li>
        <li class="brand-item">
          <img src="@/images/home/brand_03.png" />
        </li>
        <li class="brand-item">
          <img src="@/images/home/brand_05.png" />
        </li>
        <li class="brand-item">
          <img src="@/images/home/brand_07.png" />
        </li>
        <li class="brand-item">
          <img src="@/images/home/brand_09.png" />
        </li>
        <li class="brand-item">
          <img src="@/images/home/brand_11.png" />
        </li>
        <li class="brand-item">
          <img src="@/images/home/brand_13.png" />
        </li>
        <li class="brand-item">
          <img src="@/images/home/brand_15.png" />
        </li>
        <li class="brand-item">
          <img src="@/images/home/brand_17.png" />
        </li>
        <li class="brand-item">
          <img src="@/images/home/brand_19.png" />
        </li>
      </ul>
    </div>
  </div>
</template>

<script lang='ts'>
import { defineComponent } from "vue";
export default defineComponent({
    name:'Brand',
});
</script>

<style lang="less" scoped>
.brand {
  margin: 10px 0;

  .py-container {
    width: 1200px;
    margin: 0 auto;

    .brand-list {
      overflow: hidden;
      padding: 15px 0;
      background: #f7f7f7;
      line-height: 18px;

      .brand-item {
        width: 120px;
        float: left;
        height: 37px;

        img {
          border-left: 1px dotted #ccc;
          padding: 0 10px;
        }

        &:first-child {
          img {
            border-left: 0;
          }
        }
      }
    }
  }
}
</style>