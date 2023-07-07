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
            v-model="searchSoftware.title"
            placeholder="Search Drone"
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
          @click="openMenuList"
          >Add</el-button
        >
        <el-dialog
          title="Add Software"
          :visible.sync="dialogFormVisible"
          :center="true"
          width="700px"
          :lock-scroll="false"
        >
          <el-form
            ref="form"
            :model="software"
            label-width="80px"
            label-position="left"
          >
            <el-form-item label="Title" label-width="70px">
              <el-input v-model="software.title"> </el-input>
            </el-form-item>
            <el-form-item label="Version" label-width="70px">
              <el-input
                class="mo-input--number"
                v-model="software.version"
                type="number"
              >
              </el-input>
            </el-form-item>
            <el-form-item label="Menu" label-width="70px">
              <el-select
                v-model="software.type"
                placeholder="请选择Menu"
                @change="change"
              >
                <el-option
                  v-for="(item, index) in menu"
                  :key="index"
                  :label="item.name"
                  :value="item.id"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="Info" label-width="70px">
              <el-upload
                class="upload-demo"
                action="http://localhost:8080/software/upload"
                :on-success="success"
                :on-change="change"
                :file-list="fileList"
                :disabled="isAdd"
                :before-upload="beforeUpload"
                ref="myUpload"
              >
                <el-button
                  size="small"
                  type="primary"
                  style="font-size: 14px"
                  icon="el-icon-upload"
                  :disabled="isAdd"
                >
                  <span style="font-size: 14px">upload</span>
                </el-button>
                <div slot="tip" class="el-upload__tip">
                  单次最大上传数为1，且不超过10G
                </div>
              </el-upload>
            </el-form-item>
            <el-form-item label="Note" label-width="70px">
              <el-input v-model="software.note" type="textarea" :rows="5">
              </el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="cancel">Cancel</el-button>
            <el-button type="primary" @click="add" :disabled="isUpload"
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
            <el-table-column type="index" label="#" width="100">
            </el-table-column>
            <el-table-column prop="title" label="Title" width="300">
            </el-table-column>
            <el-table-column prop="version" label="Version" width="100">
            </el-table-column>
            <el-table-column prop="date" label="Date" width="150">
            </el-table-column>
            <el-table-column
              prop="downloadTimes"
              label="downloadTimes"
              width="250"
            >
            </el-table-column>
            <el-table-column prop="note" label="Note" width="300">
            </el-table-column>
            <el-table-column prop="typeName" label="Type" width="100">
            </el-table-column>
            <el-table-column align="right">
              <template slot-scope="scope">
                <el-button
                  size="small"
                  icon="el-icon-download"
                  type="primary"
                  @click="download(scope.row.id)"
                  >Download</el-button
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
          title="Delete Drone"
          :visible.sync="dialogFormDelete"
          :center="true"
          width="600px"
          :lock-scroll="false"
        >
          <el-form ref="form" label-width="80px" :disabled="true">
            <el-form-item label="Title" label-width="70px">
              <el-input v-model="editSoftware.title"></el-input>
            </el-form-item>
            <el-form-item label="Version" label-width="70px">
              <el-input
                class="mo-input--number"
                v-model="editSoftware.version"
                type="number"
              >
              </el-input>
            </el-form-item>
            <el-form-item label="Date" label-width="70px">
              <el-input v-model="editSoftware.date"></el-input>
            </el-form-item>
            <el-form-item label="Menu" label-width="70px">
              <el-select
                v-model="editSoftware.menuName"
                placeholder="请选择Menu"
                @change="change"
              >
                <el-option
                  v-for="(item, index) in menu"
                  :key="index"
                  :label="item.name"
                  :value="item.id"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="Note" label-width="70px">
              <el-input v-model="editSoftware.note" type="textarea" :rows="5">
              </el-input>
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
          title="Edit Drone"
          :visible.sync="dialogTableEdit"
          :center="true"
          width="600px"
          :lock-scroll="false"
        >
          <el-form ref="form" label-width="80px" label-position="left">
            <el-form-item label="Title" label-width="70px">
              <el-input v-model="editSoftware.title"></el-input>
            </el-form-item>
            <el-form-item label="Version" label-width="70px">
              <el-input
                class="mo-input--number"
                v-model="editSoftware.version"
                type="number"
              >
              </el-input>
            </el-form-item>
            <el-form-item label="Type" label-width="70px">
              <el-select
                v-model="menu[0].id"
                placeholder="请选择Menu"
              >
                <el-option
                  v-for="(item, index) in menu"
                  :key="index"
                  :label="item.name"
                  :value="item.id"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="Note" label-width="70px">
              <el-input v-model="editSoftware.note" type="textarea" :rows="5">
              </el-input>
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
    name: "SoftwareDrone",
    data() {
      return {
        type: 2,
        menu: [{}],
        input: "",
        tableData: [],
        dialogTableEdit: false,
        dialogFormVisible: false,
        dialogFormDelete: false,
        isDisabled: true,
        isUpload: true,
        softwaremenu: {
          name: "",
          id: "",
        },
        page: {
          pageNum: 1,
          pageSize: 10,
          total: 0,
          menuType: 2,
        },
        // form: {},
        fileList: [],
        software: {
          title: "",
          name: "",
          url: "",
          version: "",
          note: "",
          type: "",
        },
        isDisabled: false,
        isAdd: false,
        softwareId: "",
        echoInfo: {
          title: "",
          date: "",
          version: "",
          note: "",
          name: "",
        },
        editSoftware: {
          id: "",
          title: "",
          version: "",
          note: "",
          name: "",
          type: "",
          menuName: "",
        },
        searchSoftware: {
          pageNum: 1,
          pageSize: 10,
          type: 2,
          title: "",
          menuName: "",
        },
      };
    },
    methods: {
      selectClick(name) {
        this.editSoftware.menuName = name;
        console.log(name);
      },
      changeSelect(value) {
        this.editSoftware.type = value;
        this.$forceUpdate();
        console.log(this.editSoftware.menuName);
      },
      openMenuList() {
        this.dialogFormVisible = true;
      },
      menuList() {
        let that = this;
        axios({
          url: "/software/menu/front/list",
          method: "post",
          data: this.type,
          headers: {
            "Content-Type": "application/json; charset=utf-8",
          },
        }).then((res) => {
          that.menu = res.data.data;
        });
      },
      list() {
        let that = this;
        axios({
          url: "/software/list",
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
        this.software.title = response.data.title;
        this.software.url = response.data.url;
        this.software.downloadTimes = response.data.downloadTimes;
        this.software.name = response.data.name;
        this.$message.success("上传成功！");
      },
      change() {
        if (this.fileList.length > 0 && this.software.type != "") {
          this.isDisabled = false;
          this.isAdd = true;
          this.isUpload = false;
        } else {
          this.isDisabled = true;
          this.isAdd = false;
          this.isUpload = true;
        }
      },
      add() {
        if (this.fileList.length > 0) {
          this.isAdd = false;
          axios({
            url: "/software/add",
            method: "post",
            data: this.software,
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
        this.software.title = "";
        this.software.url = "";
        this.software.downloadTimes = "";
        this.name = "";
        this.version = "";
        this.note = "";
        this.type = 2;
        this.dialogFormVisible = false;
        this.isDisabled = false;
        this.isAdd = true;
        this.fileList = [];
        this.$refs.myUpload.clearFiles();
      },
      remove(id) {
        this.echo(id);
        this.softwareId = id;
        this.dialogFormDelete = true;
      },
      deleteDep() {
        let that = this;
        axios({
          url: "/software/delete",
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
          url: "/software/echo",
          method: "post",
          data: id,
          headers: {
            "Content-Type": "application/json; charset=utf-8",
          },
        }).then((res) => {
          that.editSoftware.title = res.data.data.title;
          that.editSoftware.name = res.data.data.name;
          that.editSoftware.version = res.data.data.version;
          that.editSoftware.note = res.data.data.note;
          that.editSoftware.type = res.data.data.type;
          that.editSoftware.menuName = res.data.data.menuName;
          that.editSoftware.date = res.data.data.date;
          that.editSoftware.id = id;
        });
      },
      edit(id) {
        this.dialogTableEdit = true;
        this.echo(id);
      },
      editInfo() {
        let that = this;
        axios({
          url: "/software/edit",
          method: "post",
          data: this.editSoftware,
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
        /*       // 允许上传的文件格式列表
        let acceptList = ["pdf"];
        // 根据文件名获取文件的后缀名
        let fileType = file.name.split(".").pop().toLowerCase();
        // 判断文件格式是否符合要求
        if (acceptList.indexOf(fileType) === -1) {
          this.$message.error("只能上传 pdf 格式的文件 !");
          return false;
        } */
  
        // 判断文件大小是否符合要求
        if (file.size / 1024 / 1024 > 10240) {
          this.$message.error("上传文件大小不能超过 10G !");
          return false;
        }
      },
      download(id) {
        axios({
          url: "/software/download",
          method: "post",
          data: id,
          headers: {
            "Content-Type": "application/json; charset=utf-8",
          },
        }).then((res) => {
          window.open(res.data.data);
        });
      },
      search() {
        let that = this;
        axios({
          url: "/software/search",
          method: "post",
          data: this.searchSoftware,
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
      this.menuList();
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
  .mo-input--number {
    input::-webkit-outer-spin-button,
    input::-webkit-inner-spin-button {
      -webkit-appearance: none;
    }
    input[type="number"] {
      -moz-appearance: textfield;
    }
  }
  </style>