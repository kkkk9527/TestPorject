<template>
  <div class="cart">
    <h4>全部商品</h4>
    <div class="cart-main">
      <div class="cart-th">
        <div class="cart-th1">全部</div>
        <div class="cart-th2">商品</div>
        <div class="cart-th3">单价（元）</div>
        <div class="cart-th4">数量</div>
        <div class="cart-th5">小计（元）</div>
        <div class="cart-th6">操作</div>
      </div>
      <div class="cart-body">
        <ul class="cart-list" v-for="good in cartInfoList" :key="good.id">
          <li class="cart-list-con1">
            <input
              type="checkbox"
              name="chk_list"
              :checked="good.isChecked == 1"
              @click="change(good.isChecked, good.skuId)"
            />
          </li>
          <li class="cart-list-con2">
            <img :src="good.imgUrl" />
            <div class="item-msg"></div>
          </li>
          <li class="cart-list-con3">
            <div class="item-txt">{{ good.skuName }}</div>
          </li>
          <li class="cart-list-con4">
            <span class="price">{{ good.skuPrice }}</span>
          </li>
          <li class="cart-list-con5">
            <a
              href="javascript:void(0)"
              class="mins"
              @click="increaseOrDecrease(-1, good.skuId, good.skuNum)"
              >-</a
            >
            <input
              type="text"
              :value="good.skuNum"
              minnum="1"
              class="itxt"
              @keyup.enter="
                increaseOrDecrease(
                  ($event.target.value - good.skuNum) * 1,
                  good.skuId,
                  good.skuNum
                )
              "
            />
            <a
              href="javascript:void(0)"
              class="plus"
              @click="increaseOrDecrease(1, good.skuId, good.skuNum)"
              >+</a
            >
          </li>
          <li class="cart-list-con6">
            <span class="sum">{{ good.skuPrice * good.skuNum }}</span>
          </li>
          <li class="cart-list-con7">
            <a class="sindelet" @click="deleteGood(good.skuId)">删除</a>
            <br />
            <a>移到收藏</a>
          </li>
        </ul>
      </div>
    </div>
    <div class="cart-tool">
      <div class="select-all">
        <input
          class="chooseAll"
          type="checkbox"
          :checked="allCheck"
          @change="allIsCheck(allCheck, cartInfoList)"
        />
        <span>全选</span>
      </div>
      <div class="option">
        <a href="#none">删除选中的商品</a>
        <a href="#none">移到我的关注</a>
        <a href="#none">清除下柜商品</a>
      </div>
      <div class="money-box">
        <div class="chosed">已选择 <span>0</span>件商品</div>
        <div class="sumprice">
          <em>总价（不含运费） ：</em>
          <i class="summoney">{{ totalPrice }}</i>
        </div>
        <div class="sumbtn">
          <router-link to="/trade" class="sum-btn">结算</router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import throttle from "lodash/throttle";
export default {
  name: "ShopCart",
  computed: {
    ...mapGetters("CardList", ["cartInfoList"]),
    totalPrice() {
      return this.cartInfoList.reduce(function (total, good) {
        return total + good.skuNum * good.skuPrice;
      }, 0);
    },
    allCheck() {
      let count = this.cartInfoList.reduce(function (total, good) {
        return total + good.isChecked;
      }, 0);
      if (count != this.cartInfoList.length) {
        return false;
      } else {
        return true;
      }
    },
  },
  methods: {
    /* 修改产品数量时向服务器发送数据修改并刷新 */
    increaseOrDecrease: throttle(async function (num, id, goodNum) {
      // console.log(num, id);
      try {
        switch (num) {
          case -1:
            {
              if (goodNum == 1) {
                num = 0;
              }
              await this.$store.dispatch("Detail/addToCart", {
                skuId: id,
                skuNum: num,
              });
              this.getData();
            }
            break;

          case -2:
            {
              await this.$store.dispatch("Detail/addToCart", {
                skuId: id,
                skuNum: num,
              });
              this.getData();
            }
            break;
          default:
            {
              if (typeof num == "number") {
                if (num > 0) {
                  await this.$store.dispatch("Detail/addToCart", {
                    skuId: id,
                    skuNum: num,
                  });
                  this.getData();
                } else {
                  alert("请输入正数");
                }
              }
            }
            break;
        }
      } catch (error) {
        console.log(error);
      }
    }, 1000),
    getData() {
      this.$store.dispatch("CardList/getCardList");
    },
    /* 改变选中状态 */
    async change(flag, id) {
      if (flag == 1) {
        await this.$store.dispatch("CardList/checkCart", {
          skuId: id,
          isChecked: 0,
        });
      } else {
        await this.$store.dispatch("CardList/checkCart", {
          skuId: id,
          isChecked: 1,
        });
      }
      this.getData();
      console.log(flag, id);
    },
    /* 删除商品操作 */
    async deleteGood(id) {
      try {
        await this.$store.dispatch("CardList/deletCardList", { skuId: id });
        this.getData();
        this.$forceUpdate();
      } catch (error) {
        console.log(error);
      }
    },
    async allIsCheck(flag, cartInfoList) {
      //console.log(flag, cartInfoList);
      let cartInfoArr = [];
      cartInfoList.forEach((element) => {
        let obj = { skuId: element.skuId, isChecked: flag ? 0 : 1 };
        cartInfoArr.push(obj);
      });
      await cartInfoArr.forEach((element) => {
        this.$store.dispatch("CardList/checkCart", element);
      });
      this.getData();
    },
  },
  mounted() {
    this.getData();
  },
};
</script>

<style lang="less" scoped>
.cart {
  width: 1200px;
  margin: 0 auto;

  h4 {
    margin: 9px 0;
    font-size: 14px;
    line-height: 21px;
  }

  .cart-main {
    .cart-th {
      background: #f5f5f5;
      border: 1px solid #ddd;
      padding: 10px;
      overflow: hidden;

      & > div {
        float: left;
      }

      .cart-th1 {
        width: 25%;

        input {
          vertical-align: middle;
        }

        span {
          vertical-align: middle;
        }
      }

      .cart-th2 {
        width: 25%;
      }

      .cart-th3,
      .cart-th4,
      .cart-th5,
      .cart-th6 {
        width: 12.5%;
      }
    }

    .cart-body {
      margin: 15px 0;
      border: 1px solid #ddd;

      .cart-list {
        padding: 10px;
        border-bottom: 1px solid #ddd;
        overflow: hidden;

        & > li {
          float: left;
        }

        .cart-list-con1 {
          width: 4.1667%;
        }

        .cart-list-con2 {
          width: 25%;

          img {
            width: 82px;
            height: 82px;
            float: left;
          }

          .item-msg {
            float: left;
            width: 150px;
            margin: 0 10px;
            line-height: 18px;
          }
        }

        .cart-list-con3 {
          width: 20.8333%;

          .item-txt {
            text-align: center;
          }
        }

        .cart-list-con4 {
          width: 12.5%;
        }

        .cart-list-con5 {
          width: 12.5%;

          .mins {
            border: 1px solid #ddd;
            border-right: 0;
            float: left;
            color: #666;
            width: 6px;
            text-align: center;
            padding: 8px;
          }

          input {
            border: 1px solid #ddd;
            width: 40px;
            height: 33px;
            float: left;
            text-align: center;
            font-size: 14px;
          }

          .plus {
            border: 1px solid #ddd;
            border-left: 0;
            float: left;
            color: #666;
            width: 6px;
            text-align: center;
            padding: 8px;
          }
        }

        .cart-list-con6 {
          width: 12.5%;

          .sum {
            font-size: 16px;
          }
        }

        .cart-list-con7 {
          width: 12.5%;

          a {
            color: #666;
          }
        }
      }
    }
  }

  .cart-tool {
    overflow: hidden;
    border: 1px solid #ddd;

    .select-all {
      padding: 10px;
      overflow: hidden;
      float: left;

      span {
        vertical-align: middle;
      }

      input {
        vertical-align: middle;
      }
    }

    .option {
      padding: 10px;
      overflow: hidden;
      float: left;

      a {
        float: left;
        padding: 0 10px;
        color: #666;
      }
    }

    .money-box {
      float: right;

      .chosed {
        line-height: 26px;
        float: left;
        padding: 0 10px;
      }

      .sumprice {
        width: 200px;
        line-height: 22px;
        float: left;
        padding: 0 10px;

        .summoney {
          color: #c81623;
          font-size: 16px;
        }
      }

      .sumbtn {
        float: right;

        a {
          display: block;
          position: relative;
          width: 96px;
          height: 52px;
          line-height: 52px;
          color: #fff;
          text-align: center;
          font-size: 18px;
          font-family: "Microsoft YaHei";
          background: #e1251b;
          overflow: hidden;
        }
      }
    }
  }
}
</style>