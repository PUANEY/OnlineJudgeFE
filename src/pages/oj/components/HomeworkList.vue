<template>
  <div>
    <el-table
      :data="homeworkList"
      :cell-style="cellstyle"
      v-loading="loading"
      @row-click="tohomeworkinfo"
    >
      <el-table-column
        label="作业名称"
        prop="title"
        align="center"
      ></el-table-column>
      <el-table-column
        label="开始时间"
        prop="begin_time"
        align="center"
      ></el-table-column>
      <el-table-column
        label="截止时间"
        prop="end_time"
        align="center"
      ></el-table-column
    ></el-table>
  </div>
</template>

<script>
import api from "../api.js";
export default {
  data() {
    return { homeworkList: [], loading: true };
  },
  methods: {
    getHomeworkList(courseid) {
      api.getHomework(courseid).then((res) => {
        this.homeworkList = res.data.data;
        this.loading = false;
      });
    },
    cellstyle() {
      return "cursor:pointer";
    },
    tohomeworkinfo(row) {
      api.getHomework(row.group.id, row.id).then((res) => {
        this.$emit("tohomeworkinfo", res.data.data);
      });
    },
  },
  mounted() {
    this.getHomeworkList(this.courseid);
  },
  props: {
    courseid: {
      type: String,
    },
  },
};
</script>

<style  scoped>
</style>