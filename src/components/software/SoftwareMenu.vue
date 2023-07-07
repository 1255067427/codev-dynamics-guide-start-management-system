<template>
  <div class="container">
    <!-- header start -->
    <el-header
      class="header"
      style="
        text-align: right;
        font-size: 12px;
        background-color: #d9ecff;
        border-left: 0;
      "
    >
      <div class="left">
        <el-input
          v-model="searchSoftWareMenu.name"
          placeholder="Search Menu"
        ></el-input>
        <el-button
          class="search"
          type="primary"
          icon="el-icon-search"
          @click="search"
          >Search</el-button
        >
      </div>
      <el-button
        class="right"
        type="success"
        icon="el-icon-plus"
        @click="dialogFormVisible = true"
        >Add</el-button
      >
      <el-dialog
        title="Add Menu"
        :visible.sync="dialogFormVisible"
        :center="true"
        width="600px"
        :lock-scroll="false"
      >
        <el-form
          ref="form"
          :model="softwaremenu"
          label-width="80px"
          label-position="left"
        >
          <el-form-item label="name" label-width="80px">
            <el-input v-model="softwaremenu.name" @input="changeSelect"> </el-input>
          </el-form-item>
          <el-form-item label="类型">
            <el-select
              v-model="softwaremenu.type"
              placeholder="请选择上传的类型"
              @change="changeSelect"
            >
              <el-option label="Drone" value="1"></el-option>
              <el-option label="Aviator" value="2"></el-option>
              <el-option label="Desktop" value="3"></el-option>
            </el-select>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="cancel">Cancel</el-button>
          <el-button type="primary" @click="add" :disabled="addCheckDisabled"
            >Add</el-button
          >
        </div>
      </el-dialog>
    </el-header>
    <!-- header end -->
    <!-- main start -->
    <el-main class="main">
      <div class="table" style="background-color: #fff">
        <el-table :data="tableData" height="728px">
          <el-table-column type="index" label="#" width="200">
          </el-table-column>
          <el-table-column prop="name" label="Name" width="300">
          </el-table-column>
          <el-table-column prop="date" label="Date" width="250">
          </el-table-column>
          <el-table-column
            prop="type"
            label="Type(1:Drone;2:Aviator;3:Desktop)"
            width="280"
          >
          </el-table-column>
          <el-table-column align="right">
            <template slot-scope="scope">
              <el-button
                size="small"
                icon="el-icon-edit"
                type="info"
                @click="edit(scope.row.id)"
                >Edit</el-button
              >
              <el-button
                size="small"
                icon="el-icon-delete"
                type="danger"
                @click="remove(scope.row.id)"
                >Delete</el-button
              >
            </template>
          </el-table-column>
        </el-table>
        <div class="page">
          <el-pagination
            background
            layout="prev, pager, next"
            :page-size="page.pageSize"
            :total="page.total"
            :current-page="page.pageNum"
            @current-change="pagechange"
          >
          </el-pagination>
        </div>
      </div>
      <el-dialog
        title="Delete Menu"
        :visible.sync="dialogFormDelete"
        :center="true"
        width="500px"
        :lock-scroll="false"
      >
        <el-form ref="form" label-width="80px">
          <el-form-item label="Title">
            <el-input disabled :placeholder="this.echoInfo.name"></el-input>
          </el-form-item>
          <el-form-item label="Date">
            <el-input disabled :placeholder="this.echoInfo.date"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormDelete = false">Cancel</el-button>
          <el-button icon="el-icon-delete" type="danger" @click="deleteDep"
            >Delete</el-button
          >
        </div>
      </el-dialog>
      <el-dialog
        title="Edit Menu"
        :visible.sync="dialogTableEdit"
        :center="true"
        width="500px"
        :lock-scroll="false"
      >
        <el-form ref="form" label-width="80px">
          <el-form-item label="Name">
            <el-input v-model="editSoftWareMenu.name"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogTableEdit = false">Cancel</el-button>
          <el-button icon="el-icon-edit" type="primary" @click="editInfo()"
            >Edit</el-button
          >
        </div>
      </el-dialog>
    </el-main>
    <!-- main end -->
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "SoftWareMenu",
  data() {
    return {
      input: "",
      tableData: [],
      dialogTableEdit: false,
      dialogFormVisible: false,
      dialogFormDelete: false,
      isDisabled: true,
      addCheckDisabled: true,
      page: {
        pageNum: 1,
        pageSize: 10,
        total: 0,
        type: 1,
      },
      // form: {},
      fileList: [],
      softwaremenu: {
        name: "",
        type: "",
      },
      isAdd: true,
      softwareId: "",
      echoInfo: {
        name: "",
        date: "",
      },
      editSoftWareMenu: {
        id: "",
        name: "",
      },
      searchSoftWareMenu: {
        pageNum: 1,
        pageSize: 10,
        title: "",
      },
    };
  },
  methods: {
    changeSelect() {
      if (this.softwaremenu.type != "" && this.softwaremenu.name != "") {
        this.addCheckDisabled = false;
      } else {
        this.addCheckDisabled = true;
      }
    },
    list() {
      let that = this;
      axios({
        url: "/software/menu/list",
        method: "post",
        data: this.page,
        headers: {
          "Content-Type": "application/json; charset=utf-8",
        },
      }).then((res) => {
        that.tableData = res.data.data.records;
        if (res.data.data) {
          that.page.total = res.data.data.total;
        }
      });
    },
    pagechange(num) {
      this.page.pageNum = num;
      this.list();
    },
    change() {
      if (this.softwaremenu.type != "" && this.softwaremenu.name != "") {
        this.isDisabled = false;
      } else {
        this.isDisabled = true;
      }
    },
    add() {
      this.isAdd = false;
      axios({
        url: "/software/menu/add",
        method: "post",
        data: this.softwaremenu,
        headers: {
          "Content-Type": "application/json; charset=utf-8",
        },
      }).then((res) => {
        if (res.data.code == 200) {
          this.dialogFormVisible = false;
          this.$message.success("新增成功！");
          setTimeout(() => {
            this.$router.go(0);
          }, 200);
        } else {
          this.dialogFormVisible = false;
          this.$message.error("上传失败！");
          setTimeout(() => {
            this.$router.go(0);
          }, 200);
        }
      });
    },
    cancel() {
      this.softwaremenu.name = "";
      this.softwaremenu.type = "";
      this.dialogFormVisible = false;
      this.isDisabled = false;
      this.isAdd = true;
    },
    remove(id) {
      this.echo(id);
      this.softwareId = id;
      this.dialogFormDelete = true;
    },
    deleteDep() {
      let that = this;
      axios({
        url: "/software/menu/delete",
        method: "post",
        data: this.softwareId,
        headers: {
          "Content-Type": "application/json; charset=utf-8",
        },
      }).then((res) => {
        if (res.data.code == 200) {
          this.$message.success("删除成功！");
          this.dialogFormDelete = false;
          setTimeout(() => {
            this.$router.go(0);
          }, 200);
        } else {
          this.dialogFormDelete = false;
          this.$message.error("删除失败！");
          setTimeout(() => {
            this.$router.go(0);
          }, 200);
        }
      });
    },
    echo(id) {
      let that = this;
      axios({
        url: "/software/menu/echo",
        method: "post",
        data: id,
        headers: {
          "Content-Type": "application/json; charset=utf-8",
        },
      }).then((res) => {
        that.echoInfo.name = res.data.data.name;
        that.echoInfo.date = res.data.data.date;
        that.editSoftWareMenu.id = id;
        that.editSoftWareMenu.name = that.echoInfo.name;
      });
    },
    edit(id) {
      this.dialogTableEdit = true;
      this.echo(id);
    },
    editInfo() {
      let that = this;
      axios({
        url: "/software/menu/edit",
        method: "post",
        data: this.editSoftWareMenu,
        headers: {
          "Content-Type": "application/json; charset=utf-8",
        },
      }).then((res) => {
        if (res.data.code == 200) {
          this.dialogTableEdit = false;
          this.$message.success("修改成功！");
          setTimeout(() => {
            this.$router.go(0);
          }, 200);
        } else {
          this.dialogFormVisible = false;
          this.$message.error("修改失败！");
          setTimeout(() => {
            this.$router.go(0);
          }, 200);
        }
      });
    },
    beforeUpload(file) {
      // 允许上传的文件格式列表
      let acceptList = ["pdf"];
      // 根据文件名获取文件的后缀名
      let fileType = file.name.split(".").pop().toLowerCase();
      // 判断文件格式是否符合要求
      if (acceptList.indexOf(fileType) === -1) {
        this.$message.error("只能上传 pdf 格式的文件 !");
        return false;
      }

      // 判断文件大小是否符合要求
      if (file.size / 1024 / 1024 > 10240) {
        this.$message.error("上传文件大小不能超过 10G !");
        return false;
      }
    },
    search() {
      let that = this;
      axios({
        url: "/software/menu/search",
        method: "post",
        data: this.searchSoftWareMenu,
        headers: {
          "Content-Type": "application/json; charset=utf-8",
        },
      }).then((res) => {
        that.tableData = res.data.data.records;
        if (res.data.data) {
          that.page.total = res.data.data.total;
        }
      });
    },
  },
  beforeMount() {
    this.list();
  },
};
</script>

<style lang="less">
.container {
  position: absolute;
  top: 65px;
  left: 201px;
  right: 0;
  bottom: 0;

  .header {
    display: flex;
    justify-content: space-between;

    .left {
      display: flex;
      justify-content: space-between;
      flex-direction: row;
      width: 300px;

      .search {
        height: 40px;
        margin-left: 10px;
        margin-top: 10px;
      }
    }

    .right {
      height: 40px;
      margin-left: 20px;
      margin-top: 10px;
    }
  }

  .main {
    .table {
      .page {
        display: flex;
        flex-direction: row;
        justify-content: right;
        margin-top: 12px;
      }
    }
  }
}
.el-header {
  background-color: #b3c0d1;
  color: #333;
  line-height: 60px;
}

.el-aside {
  color: #333;
}
.el-table::before {
  height: 0px;
}
.customer-table .el-table__fixed-right::before,
.el-table__fixed::before {
  width: 0;
}
</style>