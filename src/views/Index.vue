<template>
  <div class="index">
    <div class="top">
      <div class="logo">
        <el-link class="a" type="primary" @click="index">
          <el-image
            style="width: 100px; padding-left: 20px"
            :src="url"
          ></el-image>
        </el-link>
      </div>
      <div class="login">
        <el-dropdown @command="logout">
          <el-button class="loginButton" circle :disabled="true"></el-button>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item disabled>{{ this.adminName }}</el-dropdown-item>
            <el-dropdown-item @click="logout">退出登录</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
    </div>
    <el-container style="height: 800px">
      <el-aside
        width="201px"
        style="
          background-color: rgb(238, 241, 246);
          height: 870px;
          background-color: #fff;
          border-right: solid 1px rgb(238, 241, 246);
        "
        class="aside"
      >
        <el-menu
          :default-active="activeIndex"
          class="el-menu-vertical-demo"
          :router="true"
          style="border-right: 0"
        >
          <el-submenu index="/productInfo/sheet">
            <template slot="title">
              <el-image
                style="width: 18px; height: 18px; margin-right: 8px"
                :src="productInfoUrl"
              ></el-image>
              <span>Product Info</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="/productInfo/sheet">Sheet</el-menu-item>
              <el-menu-item index="/productInfo/spec">Spec</el-menu-item>
              <el-menu-item
                index="/productInfo/introduction"
                >Introduction</el-menu-item
              >
            </el-menu-item-group>
          </el-submenu>
          <el-submenu index="/videos/drone">
            <template slot="title">
              <el-image
                style="width: 18px; height: 18px; margin-right: 8px"
                :src="videosUrl"
              ></el-image>
              <span>Videos</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="/videos/drone">Drone</el-menu-item>
              <el-menu-item index="/videos/aviator">Aviator</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
          <el-submenu index="/software/menu">
            <template slot="title">
              <el-image
                style="width: 18px; height: 18px; margin-right: 8px"
                :src="softwareUrl"
              ></el-image>
              <span>SoftWare</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="/software/menu">Menu</el-menu-item>
              <el-menu-item index="/software/drone">Drone</el-menu-item>
              <el-menu-item index="/software/aviator">Aviator</el-menu-item>
              <el-menu-item index="/software/desktop">Desktop</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
          <!-- <el-menu-item index="3" @click="select3">
            <i class="el-icon-s-ticket"></i>
            <span slot="title">For Developer</span>
          </el-menu-item> -->
        </el-menu>
      </el-aside>
      <router-view></router-view>
    </el-container>
  </div>
</template>

<script>
export default {
  name: "Vue",
  components: {},
  data() {
    return {
      url: require("@/assets/images/logo.png"),
      productInfoUrl: require("@/assets/images/productInfo.png"),
      videosUrl: require("@/assets/images/bxs-videos.png"),
      softwareUrl: require("@/assets/images/Design-Software.png"),
      adminName: sessionStorage.getItem("adminName"),
      activeIndex: "/productInfo/sheet",
      selectId: sessionStorage.getItem("selectId"),
    };
  },
  methods: {
    setCurrentRoute() {
      this.activeIndex = this.$route.path; //监听到当前路由状态并激活当前菜单
    },
    index() {
      this.$router.push("/productInfo/sheet");
    },
    logout() {
      sessionStorage.clear();
      this.$router.replace("/login");
      this.$message.success("退出成功！");
    },
  },
  watch: {
    $route() {
      this.setCurrentRoute();
    },
  },
  created() {
    this.setCurrentRoute();
  },
};
</script>

<style lang="less">
.index {
  background-color: #fff;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;

  .top {
    box-sizing: border-box;
    display: flex;
    justify-content: space-between;
    height: 64px;
    background-color: #ffffff;
    padding: 16px;
    box-shadow: 0 1px 4px 0 rgb(0 21 41 / 12%);

    .logo {
      a {
        .text {
          position: relative;
          top: -9px;
          font-size: 18px;
        }
      }
    }
    .login {
      width: 50px;
      display: flex;
      justify-content: space-between;

      .loginButton {
        background-color: #cccccc;
        background-image: url(@/assets/images/login.png);
        background-repeat: no-repeat;
        background-size: cover;
      }
    }
  }
}
</style>