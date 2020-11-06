<template>
  <div>
    <Panel shadow :padding="10"
      ><div slot="title">课程详情</div>
      <div class="baseinfo">
        <div class="coursename">{{ courseinfo.group_name }}</div>
      </div>
      <HomeworkList
        :courseid="courseId"
        v-if="isHomeworkList"
        @tohomeworkinfo="tohomeworkinfo"
      ></HomeworkList>
      <HomeworkInfo v-else :homeworkinfo="homeworkinfo"></HomeworkInfo>
    </Panel>
  </div>
</template>

<script>
import HomeworkList from "../../components/HomeworkList";
import HomeworkInfo from "../../components/HomeworkInfo";
import api from "../../api.js";
export default {
  data() {
    return {
      courseId: this.$route.params.id,
      isHomeworkList: true,
      courseinfo: {},
      homeworkinfo: [],
    };
  },
  mounted() {
    api.getCourse(this.courseId).then((res) => {
      this.courseinfo = res.data.data;
    });
  },
  methods: {
    tohomeworkinfo(data) {
      this.homeworkinfo = data;
      this.isHomeworkList = false;
    },
  },
  components: {
    HomeworkList,
    HomeworkInfo,
  },
};
</script>

<style  scoped>
.baseinfo {
  position: relative;
  height: 30px;
}
.coursename {
  position: absolute;
  font-size: 30px;
  line-height: 30px;
  left: 20px;
}
</style>