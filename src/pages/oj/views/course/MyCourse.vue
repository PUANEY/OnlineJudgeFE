<template>
  <Panel shadow :padding="10">
    <div slot="title">我的课程</div>

    <div slot="extra">
      <Button
        type="primary"
        icon="ios-search"
        size="large"
        shape="circle"
        @click="searchModalVisible = true"
        >搜索课程</Button
      >
    </div>
    <el-table
      :data="mycourseList"
      v-loading="courseloading"
      :cell-style="cellstyle"
      @row-click="checkCourseInfo"
    >
      <el-table-column
        label="课程名称"
        align="center"
        prop="group_name"
      ></el-table-column>
      <el-table-column
        label="创建者"
        align="center"
        prop="teacher.username"
      ></el-table-column>
      <el-table-column label="创建时间" align="center" prop="create_time">
      </el-table-column>
    </el-table>

    <Modal
      title="搜索课程"
      v-model="searchModalVisible"
      width="650"
      @on-cancel="resetkeyword"
    >
      <Input placeholder="请输入课程名称" v-model="keyword"></Input>
      <el-table :data="courseList" v-loading="loading">
        <el-table-column prop="group_name" label="课程名字" align="center">
        </el-table-column>
        <el-table-column label="操作" align="center">
          <template slot-scope="scope">
            <el-tooltip
              class="item"
              effect="dark"
              content="加入"
              placement="top"
            >
              <el-button
                type="primary"
                icon="el-icon-plus"
                size="mini"
                circle
                @click="entrypassword(scope.row.id)"
              ></el-button>
            </el-tooltip> </template
        ></el-table-column>
      </el-table>
      <div slot="footer"></div>
    </Modal>
    <Modal
      title="加入课程"
      v-model="passworddialog"
      width="650"
      @on-cancel="resetpassword"
    >
      <Input v-model="password" placeholder="请输入课程密码"></Input>
      <div slot="footer" style="text-align: center">
        <Button type="primary" @click="entrycourse">加入</Button>
      </div>
    </Modal>
  </Panel>
</template>

<script>
import api from "../../api.js";
export default {
  data() {
    return {
      keyword: "",
      searchModalVisible: false,
      passworddialog: false,
      password: "",
      loading: true,
      courseloading: true,
      courseList: [],
      mycourseList: [],
      columns: [
        {
          title: "课程名称",
          slot: "coursename",
          align: "center",
        },
      ],
    };
  },
  mounted() {
    this.getmycourse();
  },
  methods: {
    getmycourse() {
      api.getCourse().then((res) => {
        this.mycourseList = res.data.data;
        this.courseloading = false;
      });
    },
    entrypassword(courseid) {
      this.courseid = courseid;
      this.passworddialog = true;
    },
    resetkeyword() {
      this.keyword = "";
    },
    resetpassword() {
      this.keyword = "";
      this.password = "";
    },
    entrycourse() {
      api.entryCourse(this.courseid, this.password).then((res) => {
        this.passworddialog = false;
        this.searchModalVisible = false;
        this.getmycourse();
      });
    },
    cellstyle() {
      return "cursor:pointer";
    },
    checkCourseInfo(row) {
      this.$router.push("/courseinfo/" + row.id);
    },
  },
  watch: {
    keyword(newval) {
      if (newval == "") return (this.courseList = []);
      this.loading = true;
      api.searchCourse(newval).then((res) => {
        this.loading = false;
        this.courseList = res.data.data;
      });
    },
  },
};
</script>

<style scoped lang="less">
</style>
