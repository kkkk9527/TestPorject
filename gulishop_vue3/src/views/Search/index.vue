<template>
  <div>
    <Nav />
    <div class="main">
      <div class="py-container">
        <!--bread-->
        <div class="bread">
          <ul class="fl sui-breadcrumb">
            <li>
              <a href="#">全部结果</a>
            </li>
          </ul>
          <ul class="fl sui-tag">
            <li class="with-x" v-show="categoryList.categoryName">
              {{ categoryList.categoryName }}<i @click="remove()">×</i>
            </li>
            <li class="with-x" v-if="categoryList.trademark">
              {{ categoryList.trademark.split(":")[1]
              }}<i @click="categoryList.trademark = undefined">×</i>
            </li>
            <li
              class="with-x"
              v-for="(prop, index) in categoryList.props"
              :key="index"
            >
              {{ prop.split(":")[1] }}<i @click="remove(prop)">×</i>
            </li>
          </ul>
        </div>

        <!--selector-->
        <SearchSelector
          :attrList="attrList"
          :trademarkList="trademarkList"
          :dataMerge="dataMerge"
          :addProps="addProps"
        />

        <!--details-->
        <div class="details clearfix">
          <div class="sui-navbar">
            <div class="navbar-inner filter">
              <ul class="sui-nav">
                <li
                  :class="{ active: categoryList.order.indexOf('1') != -1 }"
                  @click="changeOrder(1)"
                >
                  <a
                    >综合<i
                      class="iconfont icon-down"
                      v-show="categoryList.order.indexOf('1:desc') != -1"
                    ></i
                    ><i
                      class="iconfont icon-up"
                      v-show="categoryList.order.indexOf('1:asc') != -1"
                    ></i
                  ></a>
                </li>
                <li
                  :class="{ active: categoryList.order.indexOf('2') != -1 }"
                  @click="changeOrder(2)"
                >
                  <a
                    >价格<i
                      class="iconfont icon-down"
                      v-show="categoryList.order.indexOf('2:desc') != -1"
                    ></i
                    ><i
                      class="iconfont icon-up"
                      v-show="categoryList.order.indexOf('2:asc') != -1"
                    ></i
                  ></a>
                </li>
              </ul>
            </div>
          </div>
          <div class="goods-list">
            <ul class="yui3-g">
              <li class="yui3-u-1-5" v-for="good in goodList" :key="good.id">
                <div class="list-wrap" @click="goDetail(good.id)">
                  <div class="p-img">
                    <a><img :src="good.defaultImg" /></a>
                  </div>
                  <div class="price">
                    <strong>
                      <em>¥</em>
                      <i>{{ good.price }}</i>
                    </strong>
                  </div>
                  <div class="attr">
                    <a>{{ good.title }}</a>
                  </div>
                  <div class="commit">
                    <i class="command">已有<span>2000</span>人评价</i>
                  </div>
                  <div class="operate">
                    <a
                      href="success-cart.html"
                      target="_blank"
                      class="sui-btn btn-bordered btn-danger"
                      >加入购物车</a
                    >
                    <a href="javascript:void(0);" class="sui-btn btn-bordered"
                      >收藏</a
                    >
                  </div>
                </div>
              </li>
            </ul>
          </div>
          <div class="fr page">
            <el-pagination
              layout="prev, pager, next"
              background
              :page-count="totalPage"
              v-model:currentPage="categoryList.pageNo"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang='ts'>
import {
  defineComponent,
  onUnmounted,
  reactive,
  onMounted,
  computed,
  watch,
  ref,
} from "vue";
import { useRoute, useRouter } from "vue-router";
import { useStore } from "vuex";
import SearchSelector from "./SearchSelector/SearchSelector.vue";
import Nav from "@/components/Nav.vue";

interface searchData {
  category1Id?: undefined | string;
  category2Id?: undefined | string;
  category3Id?: undefined | string;
  categoryName?: undefined | string;
  keyword?: undefined | string;
  props: any[];
  trademark: undefined | string;
  order: string;
  pageNo: number;
  pageSize: number;
}

export default defineComponent({
  name: "Search",
  setup() {
    let router = useRouter(),
      route = useRoute(),
      store = useStore(),
      orderReason = 1,
      upOrdown = ref(1);
    /* 请求商品的参数 */
    const categoryList: searchData = reactive({
      category1Id: undefined,
      category2Id: undefined,
      category3Id: undefined,
      categoryName: undefined,
      keyword: undefined,
      props: [],
      trademark: undefined,
      order: "1:desc",
      pageNo: 1,
      pageSize: 10,
    });
    categoryList.order.split(":");
    /* 移除面包屑中的参数 */
    function remove(name?: string) {
      if (!name) {
        let obj = {
          category1Id: undefined,
          category2Id: undefined,
          category3Id: undefined,
          categoryName: undefined,
        };
        router.push({
          name: "search",
          query: obj,
        });
      } else {
        categoryList.props.splice(categoryList.props.indexOf(name), 1);
      }
    }
    /* 修改排序方式 */
    function changeOrder(num: number): void {
      let first = categoryList.order.split(":")[0],
        second = categoryList.order.split(":")[1];
      switch (num) {
        case 1:
          {
            if (first === "1") {
              if (second === "desc") {
                categoryList.order = "1:asc";
              } else {
                categoryList.order = "1:desc";
              }
            } else {
              categoryList.order = "1:desc";
            }
            sessionStorage.setItem("order", categoryList.order);
          }
          break;
        case 2:
          {
            if (first === "2") {
              if (second === "desc") {
                categoryList.order = "2:asc";
              } else {
                categoryList.order = "2:desc";
              }
            } else {
              categoryList.order = "2:desc";
            }
            sessionStorage.setItem("order", categoryList.order);
          }
          break;
      }
    }
    /* 合并请求参数 */
    function dataMerge<T>(data1: T): void {
      let obj = { trademark: data1 };
      Object.assign(categoryList, obj);
    }
    /* 添加props属性 */
    function addProps(prop: string): void {
      categoryList.props.push(prop);
      sessionStorage.setItem("props", categoryList.props.toString());
    }
    /* 跳转至商品详细信息页面 */
    function goDetail(id: number): void {
      router.push({
        name: "detail",
        query: { skuId: id },
      });
    }
    /* 挂载组件时请求数据 */
    onMounted(() => {
      let obj: searchData = {
        props: [],
        trademark: sessionStorage.getItem("trademark") || undefined,
        order: sessionStorage.getItem("order") || "1:desc",
        pageNo: 1,
        pageSize: 10,
      };
      let prop = sessionStorage.getItem("props");
      if (prop) {
        obj.props = prop.split(",");
      }
      Object.assign(categoryList, route.query, obj);
      // store.dispatch("search/GetGoodList", categoryList);
    });
    onUnmounted(() => {
      sessionStorage.clear();
    });
    /* 监视地址变化并向后台请求数据 */
    watch(
      () => route.path,
      () => {
        let obj = {
          category1Id: undefined,
          category2Id: undefined,
          category3Id: undefined,
          categoryName: undefined,
        };
        Object.assign(obj, route.query);
        Object.assign(categoryList, obj);
        //store.dispatch("search/GetGoodList", categoryList);
      },
      { deep: true }
    );
    /* 监视请求参数对象 */
    watch(
      categoryList,
      () => {
        store.dispatch("search/GetGoodList", categoryList);
      },
      { deep: true }
    );
    return {
      //  品牌列表
      trademarkList: computed(() => {
        return store.getters["search/GetTrademarkList"];
      }),
      // 属性列表
      attrList: computed(() => {
        return store.getters["search/GetAttrsList"];
      }),
      // 商品列表
      goodList: computed(() => {
        return store.getters["search/GetGoodList"];
      }),
      // 总页数
      totalPage: computed(() => {
        return store.state.search.goodList.totalPages;
      }),
      // 每页显示数量
      pageSize: computed(() => {
        return store.state.search.goodList.pageSize;
      }),
      // 当前页数
      pageNo: computed(() => {
        return store.state.search.goodList.pageNo;
      }),
      dataMerge,
      categoryList,
      remove,
      addProps,
      changeOrder,
      goDetail,
    };
  },
  /* watch: { */
  /* setup()中无法监视地址的变化 */
  /* $route() {
      let data = {};
      Object.assign(data, this.categoryList, this.$route.query);
      this.GetGoodList(data);
      console.log(data);
    },
  },
  methods: {
    ...mapActions('search',['GetGoodList'])
  }, */
  components: {
    SearchSelector,
    Nav,
  },
});
</script>

<style lang="less" scoped>
.main {
  margin: 10px 0;

  .py-container {
    width: 1200px;
    margin: 0 auto;

    .bread {
      margin-bottom: 5px;
      overflow: hidden;

      .sui-breadcrumb {
        padding: 3px 15px;
        margin: 0;
        font-weight: 400;
        border-radius: 3px;
        float: left;

        li {
          display: inline-block;
          line-height: 18px;

          a {
            color: #666;
            text-decoration: none;

            &:hover {
              color: #4cb9fc;
            }
          }
        }
      }

      .sui-tag {
        margin-top: -5px;
        list-style: none;
        font-size: 0;
        line-height: 0;
        padding: 5px 0 0;
        margin-bottom: 18px;
        float: left;

        .with-x {
          font-size: 12px;
          margin: 0 5px 5px 0;
          display: inline-block;
          overflow: hidden;
          color: #000;
          background: #f7f7f7;
          padding: 0 7px;
          height: 20px;
          line-height: 20px;
          border: 1px solid #dedede;
          white-space: nowrap;
          transition: color 400ms;
          cursor: pointer;

          i {
            margin-left: 10px;
            cursor: pointer;
            font: 400 14px tahoma;
            display: inline-block;
            height: 100%;
            vertical-align: middle;
          }

          &:hover {
            color: #28a3ef;
          }
        }
      }
    }

    .details {
      margin-bottom: 5px;

      .sui-navbar {
        overflow: visible;
        margin-bottom: 0;

        .filter {
          min-height: 40px;
          padding-right: 20px;
          background: #fbfbfb;
          border: 1px solid #e2e2e2;
          padding-left: 0;
          border-radius: 0;
          box-shadow: 0 1px 4px rgba(0, 0, 0, 0.065);

          .sui-nav {
            position: relative;
            left: 0;
            display: block;
            float: left;
            margin: 0 10px 0 0;

            li {
              float: left;
              line-height: 18px;

              a {
                display: block;
                cursor: pointer;
                padding: 11px 15px;
                color: #777;
                text-decoration: none;
              }

              &.active {
                a {
                  background: #e1251b;
                  color: #fff;
                }
              }
            }
          }
        }
      }

      .goods-list {
        margin: 20px 0;

        ul {
          display: flex;
          flex-wrap: wrap;

          li {
            height: 100%;
            width: 20%;
            margin-top: 10px;
            line-height: 28px;

            .list-wrap {
              .p-img {
                padding-left: 15px;
                width: 215px;
                height: 255px;

                a {
                  color: #666;

                  img {
                    max-width: 100%;
                    height: auto;
                    vertical-align: middle;
                  }
                }
              }

              .price {
                padding-left: 15px;
                font-size: 18px;
                color: #c81623;

                strong {
                  font-weight: 700;

                  i {
                    margin-left: -5px;
                  }
                }
              }

              .attr {
                padding-left: 15px;
                width: 85%;
                overflow: hidden;
                margin-bottom: 8px;
                min-height: 38px;
                cursor: pointer;
                line-height: 1.8;
                display: -webkit-box;
                -webkit-box-orient: vertical;
                -webkit-line-clamp: 2;

                a {
                  color: #333;
                  text-decoration: none;
                }
              }

              .commit {
                padding-left: 15px;
                height: 22px;
                font-size: 13px;
                color: #a7a7a7;

                span {
                  font-weight: 700;
                  color: #646fb0;
                }
              }

              .operate {
                padding: 12px 15px;

                .sui-btn {
                  display: inline-block;
                  padding: 2px 14px;
                  box-sizing: border-box;
                  margin-bottom: 0;
                  font-size: 12px;
                  line-height: 18px;
                  text-align: center;
                  vertical-align: middle;
                  cursor: pointer;
                  border-radius: 0;
                  background-color: transparent;
                  margin-right: 15px;
                }

                .btn-bordered {
                  min-width: 85px;
                  background-color: transparent;
                  border: 1px solid #8c8c8c;
                  color: #8c8c8c;

                  &:hover {
                    border: 1px solid #666;
                    color: #fff !important;
                    background-color: #666;
                    text-decoration: none;
                  }
                }

                .btn-danger {
                  border: 1px solid #e1251b;
                  color: #e1251b;

                  &:hover {
                    border: 1px solid #e1251b;
                    background-color: #e1251b;
                    color: white !important;
                    text-decoration: none;
                  }
                }
              }
            }
          }
        }
      }

      .page {
        width: 733px;
        height: 66px;
        overflow: hidden;
        float: right;

        .sui-pagination {
          margin: 18px 0;

          ul {
            margin-left: 0;
            margin-bottom: 0;
            vertical-align: middle;
            width: 490px;
            float: left;

            li {
              line-height: 18px;
              display: inline-block;

              a {
                position: relative;
                float: left;
                line-height: 18px;
                text-decoration: none;
                background-color: #fff;
                border: 1px solid #e0e9ee;
                margin-left: -1px;
                font-size: 14px;
                padding: 9px 18px;
                color: #333;
              }

              &.active {
                a {
                  background-color: #fff;
                  color: #e1251b;
                  border-color: #fff;
                  cursor: default;
                }
              }

              &.prev {
                a {
                  background-color: #fafafa;
                }
              }

              &.disabled {
                a {
                  color: #999;
                  cursor: default;
                }
              }

              &.dotted {
                span {
                  margin-left: -1px;
                  position: relative;
                  float: left;
                  line-height: 18px;
                  text-decoration: none;
                  background-color: #fff;
                  font-size: 14px;
                  border: 0;
                  padding: 9px 18px;
                  color: #333;
                }
              }

              &.next {
                a {
                  background-color: #fafafa;
                }
              }
            }
          }

          div {
            color: #333;
            font-size: 14px;
            float: right;
            width: 241px;
          }
        }
      }
    }
  }
}
</style>