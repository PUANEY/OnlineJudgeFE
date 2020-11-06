<template>
  <div class="courseinfo">
    <Panel title="Course Info">
      <div class="baseinfo">
        <div class="coursename">{{ courseinfo.group_name }}</div>
        <div class="studentnum">学生人数:{{ courseinfo.total }}</div>
        <div class="createtime">{{ courseinfo.create_time }}</div>
        <div class="entrycode">课程密码:{{ courseinfo.entry_code }}</div>
      </div>
      <HomeworkList
        :courseid="courseid"
        v-if="isHomeworkList"
        @toHomeworkInfo="toHomeworkInfo"
      ></HomeworkList>
      <HomeworkInfo v-else :homeworkInfo="homeworkInfo"></HomeworkInfo>
    </Panel>
  </div>
</template>

<script>
import api from "../../api.js";
import HomeworkList from "../../components/HomeworkList";
import HomeworkInfo from "../../components/HomeworkInfo";
export default {
  data() {
    return {
      courseid: this.$route.params.id,
      courseinfo: {},
      isHomeworkList: true,
      homeworkInfo: {},
    };
  },
  methods: {
    toHomeworkInfo(data) {
      this.homeworkInfo = data;
      this.isHomeworkList = false;
    },
  },
  mounted() {
    api.getCourse(this.courseid).then((res) => {
      this.courseinfo = res.data.data;
    });
  },
  components: {
    HomeworkList,
    HomeworkInfo,
  },
};
</script>

<style  scoped>
.baseinfo {
  height: 80px;
  position: relative;
}
.coursename {
  position: absolute;
  font-size: 30px;
  left: 20px;
}
.studentnum {
  position: absolute;
  right: 38px;
  top: 15px;
  font-size: 15px;
}
.createtime {
  position: absolute;
  top: 40px;
  left: 20px;
  font-size: 16px;
  color: rgb(122, 122, 122);
}
.entrycode {
  position: absolute;
  top: 40px;
  right: 20px;
  color: rgb(122, 122, 122);
}
</style>