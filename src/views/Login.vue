<template>
  <div class="login-container">
    <el-form class="form" ref="form" :model="form">
      <div class="logo">
        <el-image style="width: 200px; height: 200px" :src="url"></el-image>
        <div>员工管理平台</div>
      </div>
      <el-form-item>
        <el-input
          v-model="form.adminName"
          placeholder="用户名"
          prefix-icon="el-icon-s-custom"
          autofocus
          clearable
        ></el-input>
      </el-form-item>
      <el-form-item>
        <el-input
          v-model="form.adminPassword"
          placeholder="密   码"
          show-password
          prefix-icon="el-icon-s-cooperation"
        ></el-input>
      </el-form-item>
      <el-form-item>
        <div class="button">
          <div class="login">
            <el-button class="loginButton" type="primary" @click="login"
              >登录</el-button
            >
          </div>
          <div>
            <router-link to="/register">
              <el-button type="danger" @click="register">注册</el-button>
            </router-link>
          </div>
        </div>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "Login",
  data() {
    return {
      url: require("@/assets/images/logo.png"),
      form: {
        adminName: "",
        adminPassword: "",
      },
    };
  },
  methods: {
    login() {
      let that = this;
      axios({
        url: "http://localhost:8080/admin/login", //后台接口
        method: "post",
        data: this.form,
        headers: {
          "Content-Type": "application/json; charset=utf-8",
        },
      }).then(function (res) {
        console.log(res);
        if (res.data.code === "002") {
          sessionStorage.setItem("id", res.data.data.id);
          sessionStorage.setItem("adminName", res.data.data.adminName);
          console.log(res);
          that.$message.success("登录成功！");
          that.$router.push("/employee");
          that.$router.go(0)("/employee")
        } else {
          that.$message.error("账号密码错误，登录失败!");
        }
      });
    },
    register() {
      console.log("注册!");
    },
  },
};
</script>

<style lang="less" scoped>
.login-container {
  display: flex;
  // min-height:100vh;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: url(@/assets/images/background.png);
  .form {
    margin: auto;
    font-size: 20px;
    padding: 20px;
    padding-right: 40px;
    line-height: 20px;
    width: 300px;
    box-shadow: 1px 1px 10px 0 #eee;

    .logo {
      display: flex;
      justify-content: space-around;
      flex-direction: column;
      padding-bottom: 30px;
      text-align: center;
      align-items: center;
      color: #1296db;
      font-size: 25px;
      font-weight: bold;
    }
    .button {
      display: flex;
      justify-content: left;

      .login {
        flex: 1;
        .loginButton {
          width: 210px;
        }
      }
    }
  }
}
</style>