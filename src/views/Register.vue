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
          placeholder="新用户名"
          prefix-icon="el-icon-s-custom"
          autofocus
          clearable
        ></el-input>
      </el-form-item>
      <el-form-item>
        <el-input
          v-model="form.adminPassword1"
          placeholder="新密码"
          show-password
          prefix-icon="el-icon-s-cooperation"
        ></el-input>
      </el-form-item>
      <el-form-item>
        <el-input
          v-model="form.adminPassword2"
          placeholder="确认密码"
          show-password
          prefix-icon="el-icon-s-cooperation"
        ></el-input>
      </el-form-item>
      <el-form-item>
        <div class="button">
          <router-link to="/login">
            <el-button class="loginButton" type="primary">登录</el-button>
          </router-link>
          <div class="register">
            <div>
              <el-button class="registerButton" type="danger" @click="register"
                >注册</el-button
              >
            </div>
          </div>
        </div>
      </el-form-item>
    </el-form>
  </div>
</template>
  
  <script>
import axios from "axios";
export default {
  name: "Register",
  data() {
    return {
      url: require("@/assets/images/logo.png"),
      form: {
        adminName: "",
        adminPassword1: "",
        adminPassword2: "",
      },
    };
  },
  methods: {
    register() {
      let that = this;
      axios({
        url: "http://localhost:8080/admin/register", //后台接口
        method: "post",
        data: this.form,
        headers: {
          "Content-Type": "application/json; charset=utf-8",
        },
      }).then(function (res) {
        if (res.data.code === "002") {
            that.$router.push("/login");
            that.$message.success("注册成功，请登录！");
        } else {
          if (
            that.form.adminName === null ||
            that.form.adminPassword1 === null ||
            that.form.adminPassword2 === null ||
            that.form.adminName.trim().length === 0 ||
            that.form.adminPassword1.trim().length === 0 ||
            that.form.adminPassword2.trim().length === 0
          ) {
            that.$message.error("账号密码为空，注册失败！");
          } else if (that.form.adminPassword1 !== that.form.adminPassword2) {
            that.$message.error("两次输入密码不同，注册失败！");
          }else{
            that.$message.error("账号已存在，注册失败！");
          }
        }
      });
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
      justify-content: space-between;

      .registerButton {
        width: 210px;
      }
    }
  }
}
</style>