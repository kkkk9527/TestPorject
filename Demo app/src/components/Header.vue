<template>
  <header class="header">
    <!-- 头部的第一行 -->
    <div class="top">
      <div class="container">
        <div class="loginList" v-show="!userInfo">
          <p>尚品汇欢迎您！</p>
          <p>
            <span>请</span>
            <router-link :to="{ path: '/Login' }">登录</router-link>
            <router-link to="/Register" class="register">免费注册</router-link>
          </p>
        </div>
        <div class="loginList" v-show="userInfo">
          <p>尚品汇欢迎您！</p>
          <p>
            <span>请</span>
            <router-link :to="{ path: '/Login' }">{{ userInfo }}</router-link>
            <a class="register" @click="loginOut">{{
              "退出"
            }}</a>
          </p>
        </div>
        <div class="typeList">
          <router-link to="/center">我的订单</router-link>
          <router-link to="/trade">我的购物车</router-link>
          <a href="###">我的尚品汇</a>
          <a href="###">尚品汇会员</a>
          <a href="###">企业采购</a>
          <a href="###">关注尚品汇</a>
          <a href="###">合作招商</a>
          <a href="###">商家后台</a>
        </div>
      </div>
    </div>
    <!--头部第二行 搜索区域-->
    <div class="bottom">
      <h1 class="logoArea">
        <a class="logo" title="尚品汇" href="#home" target="_blank">
          <img src="@/images/Logo.png" alt="" />
        </a>
      </h1>
      <div class="searchArea">
        <form action="###" class="searchForm">
          <input
            type="text"
            id="autocomplete"
            class="input-error input-xxlarge"
            v-model="keyword"
          />
          <button
            class="sui-btn btn-xlarge btn-danger"
            type="button"
            @click="search"
          >
            搜索
          </button>
        </form>
      </div>
    </div>
  </header>
</template>

<script>
import { mapGetters } from "vuex";
export default {
  name: "Header",
  data() {
    return {
      keyword: "",
      paramsData: {},
    };
  },
  computed: {
    ...mapGetters("RegisterAndLogin", ["userInfo"]),
  },
  methods: {
    search() {
      let local = { name: "Search" };
      let query = JSON.parse(JSON.stringify(this.$route.query)); //保留原本路由中的参数
      query.keyword = this.keyword;
      local.query = query;
      this.$store.commit("Search/MERGEDATA", query); //修改vuex中的数据
      this.$router.push(local);
      this.$bus.$emit("addKeyWord", this.keyword);
      this.$store.dispatch("Search/SearchInfo");
    },
    // 登出
    async loginOut(){
      try{
        await this.$store.dispatch('RegisterAndLogin/LoginOut');
        this.$router.push('/home');
      }catch(error){
        alert(error.message);
      }
    }
  },
  mounted() {
  },
};
</script>

<style lang="less" scoped>
.header {
  & > .top {
    background-color: #eaeaea;
    height: 30px;
    line-height: 30px;

    .container {
      width: 1200px;
      margin: 0 auto;
      overflow: hidden;

      .loginList {
        float: left;

        p {
          float: left;
          margin-right: 10px;

          .register {
            border-left: 1px solid #b3aeae;
            padding: 0 5px;
            margin-left: 5px;
          }
        }
      }

      .typeList {
        float: right;

        a {
          padding: 0 10px;

          & + a {
            border-left: 1px solid #b3aeae;
          }
        }
      }
    }
  }

  & > .bottom {
    width: 1200px;
    margin: 0 auto;
    overflow: hidden;

    .logoArea {
      float: left;

      .logo {
        img {
          width: 175px;
          margin: 25px 45px;
        }
      }
    }

    .searchArea {
      float: right;
      margin-top: 35px;

      .searchForm {
        overflow: hidden;

        input {
          box-sizing: border-box;
          width: 490px;
          height: 32px;
          padding: 0px 4px;
          border: 2px solid #ea4a36;
          float: left;

          &:focus {
            outline: none;
          }
        }

        button {
          height: 32px;
          width: 68px;
          background-color: #ea4a36;
          border: none;
          color: #fff;
          float: left;
          cursor: pointer;

          &:focus {
            outline: none;
          }
        }
      }
    }
  }
}
</style>>