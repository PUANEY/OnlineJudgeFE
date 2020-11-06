<template>
  <div>
    <el-button type="primary" round @click="createHomeworkdialog = true"
      >创建作业</el-button
    >
    <el-table
      :data="homeworklist"
      stripe
      @cell-click="toHomeworkInfo"
      :cell-style="cellStyle"
    >
      <el-table-column
        label="作业名称"
        align="center"
        prop="title"
      ></el-table-column>
      <el-table-column
        label="开始时间"
        align="center"
        prop="begin_time"
      ></el-table-column>
      <el-table-column
        label="截止时间"
        align="center"
        prop="end_time"
      ></el-table-column>
      <el-table-column label="操作" align="center">
        <template slot-scope="scope">
          <el-tooltip class="item" effect="dark" content="删除" placement="top">
            <el-button
              type="danger"
              icon="el-icon-delete"
              size="mini"
              circle
              @click="deleteHomeworkById(scope.row.id)"
            ></el-button>
          </el-tooltip>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog title="创建作业" :visible.sync="createHomeworkdialog" center>
      <el-form
        label-position="right"
        label-width="80px"
        :model="createHomeworkForm"
        :rules="rules"
        ref="createHomeworkForm"
      >
        <el-form-item label="作业名称" prop="name">
          <el-input v-model="createHomeworkForm.name"></el-input>
        </el-form-item>
        <el-form-item label="截止日期" prop="deadline">
          <el-date-picker
            v-model="createHomeworkForm.deadline"
            type="date"
            placeholder="选择日期"
          >
          </el-date-picker>
        </el-form-item>
      </el-form>
      <div style="text-align: center">
        <el-button type="primary" @click="createHomework">创建</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import api from "../api.js";
export default {
  data() {
    return {
      homeworklist: [],
      createHomeworkForm: {
        name: "",
        deadline: "",
      },
      createHomeworkdialog: false,
      rules: {
        name: [{ required: true, message: "请输入作业名称", trigger: "blur" }],
        deadline: [
          { required: true, message: "请输入截止日期", trigger: "blur" },
        ],
      },
    };
  },
  props: {
    courseid: {
      type: String,
    },
  },
  mounted() {
    this.getHomeworkList(this.courseid);
  },
  methods: {
    getHomeworkList(group_id) {
      api.getHomework(group_id).then((res) => {
        this.homeworklist = res.data.data;
      });
    },
    createHomework() {
      this.$refs.createHomeworkForm.validate((res) => {
        if (!res) {
          return;
        }
        api
          .createHomework(
            this.courseid,
            this.createHomeworkForm.name,
            this.createHomeworkForm.deadline
          )
          .then((res) => {
            this.getHomeworkList(this.courseid);
            this.createHomeworkdialog = false;
          });
      });
    },
    deleteHomeworkById(homeworkid) {
      this.$confirm("此操作将永久删除该作业, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          api.deleteHomework(homeworkid).then((res) => {
            this.getHomeworkList(this.courseid);
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
    cellStyle() {
      return "cursor:pointer";
    },
    toHomeworkInfo(row, cell) {
      if (cell.label == "操作") {
        return;
      }

      api.getHomework(row.group.id, row.id).then((res) => {
        this.$emit("toHomeworkInfo", res.data.data);
      });
    },
  },
};
</script>

<style  scoped>
</style>