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
          v-model="searchProductInfo.title"
          placeholder="Search Spec"
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
        title="Add Spec"
        :visible.sync="dialogFormVisible"
        :center="true"
        width="600px"
        :lock-scroll="false"
      >
        <el-form
          ref="form"
          :model="productInfo"
          label-width="80px"
          label-position="left"
        >
          <el-form-item label="Title" label-width="50px">
            <el-input v-model="productInfo.title"> </el-input>
          </el-form-item>
          <el-form-item label="Info" label-width="50px">
            <el-upload
              class="upload-demo"
              action="http://localhost:8080/productInfo/upload"
              :on-success="success"
              :on-change="change"
              :file-list="fileList"
              :disabled="this.isDisabled"
              accept=".pdf"
              :before-upload="beforeUpload"
              ref="myUpload"
            >
              <el-button
                size="small"
                type="primary"
                style="font-size: 14px"
                icon="el-icon-upload"
                :disabled="this.isDisabled"
              >
                <span style="font-size: 14px">upload</span>
              </el-button>
              <div slot="tip" class="el-upload__tip">
                单次最大上传数为1，且不超过10G
              </div>
            </el-upload>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="cancel">Cancel</el-button>
          <el-button type="primary" @click="add" :disabled="isAdd"
            >Upload</el-button
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
          <el-table-column prop="title" label="Title" width="300">
          </el-table-column>
          <el-table-column prop="date" label="Date" width="250">
          </el-table-column>
          <el-table-column
            prop="referenceTimes"
            label="referenceTimes"
            width="250"
          >
          </el-table-column>
          <el-table-column
            prop="downloadTimes"
            label="downloadTimes"
            width="250"
          >
          </el-table-column>
          <el-table-column align="right">
            <template slot-scope="scope">
              <el-button
                size="small"
                icon="el-icon-view"
                type="primary"
                @click="reference(scope.row.id)"
                >Reference</el-button
              >
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
        title="Delete Spec"
        :visible.sync="dialogFormDelete"
        :center="true"
        width="500px"
        :lock-scroll="false"
      >
        <el-form ref="form" label-width="80px">
          <el-form-item label="Title">
            <el-input disabled :placeholder="this.echoInfo.title"></el-input>
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
        title="Edit Spec"
        :visible.sync="dialogTableEdit"
        :center="true"
        width="500px"
        :lock-scroll="false"
      >
        <el-form ref="form" label-width="80px">
          <el-form-item label="Title">
            <el-input v-model="editProductInfo.title"></el-input>
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
  name: "ProductInfoSpec",
  data() {
    return {
      input: "",
      tableData: [],
      dialogTableEdit: false,
      dialogFormVisible: false,
      dialogFormDelete: false,
      page: {
        pageNum: 1,
        pageSize: 10,
        total: 0,
        type: 3,
      },
      // form: {},
      fileList: [],
      productInfo: {
        title: "",
        name: "",
        url: "",
        type: 3,
      },
      isDisabled: false,
      isAdd: true,
      productInfoId: "",
      echoInfo: {
        title: "",
        date: "",
      },
      editProductInfo: {
        id: "",
        title: "",
      },
      searchProductInfo: {
        pageNum: 1,
        pageSize: 10,
        type: 3,
        title: "",
      },
    };
  },
  methods: {
    list() {
      let that = this;
      axios({
        url: "/productInfo/list",
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
    success(response, file, fileList) {
      this.fileList = fileList.slice(-3);
      this.productInfo.title = response.data.title;
      this.productInfo.url = response.data.url;
      this.productInfo.referenceTimes = response.data.referenceTimes;
      this.productInfo.downloadTimes = response.data.downloadTimes;
      this.productInfo.name = response.data.name;
      this.$message.success("上传成功！");
    },
    change() {
      if (this.fileList.length > 0) {
        this.isDisabled = true;
        this.isAdd = false;
      }
    },
    add() {
      if (this.fileList.length > 0) {
        this.isAdd = false;
        axios({
          url: "/productInfo/add",
          method: "post",
          data: this.productInfo,
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
      } else {
        this.isAdd = true;
      }
    },
    cancel() {
      this.fileList = [];
      this.productInfo.title = "";
      this.productInfo.url = "";
      this.productInfo.referenceTimes = "";
      this.productInfo.downloadTimes = "";
      this.dialogFormVisible = false;
      this.isDisabled = false;
      this.isAdd = true;
      this.$refs.myUpload.clearFiles()
    },
    remove(id) {
      this.echo(id);
      this.productInfoId = id;
      this.dialogFormDelete = true;
    },
    deleteDep() {
      let that = this;
      axios({
        url: "/productInfo/delete",
        method: "post",
        data: this.productInfoId,
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
        url: "/productInfo/echo",
        method: "post",
        data: id,
        headers: {
          "Content-Type": "application/json; charset=utf-8",
        },
      }).then((res) => {
        that.echoInfo.title = res.data.data.title;
        that.echoInfo.date = res.data.data.date;
        that.editProductInfo.id = id;
        that.editProductInfo.title = that.echoInfo.title;
      });
    },
    edit(id) {
      this.dialogTableEdit = true;
      this.echo(id);
    },
    editInfo() {
      let that = this;
      axios({
        url: "/productInfo/edit",
        method: "post",
        data: this.editProductInfo,
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
    reference(id) {
      axios({
        url: "/productInfo/reference",
        method: "post",
        data: id,
        headers: {
          "Content-Type": "application/json; charset=utf-8",
        },
      }).then((res) => {
        window.open(res.data.msg);
      });
    },
    search() {
      let that = this;
      axios({
        url: "/productInfo/search",
        method: "post",
        data: this.searchProductInfo,
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