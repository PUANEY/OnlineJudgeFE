<template>
  <div>
    <div class="homeworkName">
      <span style="margin: 0 20px">{{
        this.newhomeworkInfo[0].homework_detail.title
      }}</span>
      <el-button type="primary" @click="addProblemDialogVisible = true" round
        >添加题目</el-button
      >
    </div>
    <el-container>
      <el-container>
        <el-table :data="this.newhomeworkInfo[1].problem_list" stripe border>
          <el-table-column
            type="index"
            label="序号"
            align="center"
            width="100px"
          ></el-table-column>
          <el-table-column
            prop="id"
            label="id"
            align="center"
          ></el-table-column>
          <el-table-column
            prop="title"
            label="题目名称"
            align="center"
          ></el-table-column>
          <el-table-column label="操作" align="center">
            <template slot-scope="{ row }">
              <el-tooltip
                class="item"
                effect="dark"
                content="查重"
                placement="top"
              >
                <el-button
                  type="warning"
                  icon="el-icon-search"
                  size="mini"
                  circle
                  @click="
                    opencodecheck(
                      newhomeworkInfo[0].homework_detail.group.id,
                      newhomeworkInfo[0].homework_detail.id,
                      row._id
                    )
                  "
                ></el-button>
              </el-tooltip>
              <el-tooltip
                class="item"
                effect="dark"
                content="删除"
                placement="top"
              >
                <el-button
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                  circle
                  @click="
                    deleteProblem(
                      newhomeworkInfo[0].homework_detail.id,
                      scope.row._id
                    )
                  "
                ></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>
      </el-container>
      <el-container class="studentlist">
        <el-table :data="this.newhomeworkInfo[2].al_achieve" stripe border>
          <el-table-column
            type="index"
            label="序号"
            align="center"
            width="100px"
          ></el-table-column>
          <el-table-column prop="username" label="已完成姓名" align="center">
          </el-table-column>
        </el-table>
      </el-container>
      <el-container class="studentlist">
        <el-table :data="this.newhomeworkInfo[3].no_achieve" stripe border>
          <el-table-column
            type="index"
            label="序号"
            align="center"
            width="100px"
          ></el-table-column>
          <el-table-column prop="username" label="未完成姓名" align="center">
          </el-table-column>
        </el-table>
      </el-container>
    </el-container>
    <!-- 作业中题目列表 ↑ -->

    <el-dialog title="添加题目" center :visible.sync="addProblemDialogVisible">
      <div>
        <el-input
          v-model="keyword"
          placeholder="关键字"
          prefix-icon="el-icon-search"
        >
        </el-input>
        <el-table :data="problems" v-loading="loading">
          <el-table-column label="ID" width="100" prop="id" align="center">
          </el-table-column>
          <el-table-column label="题目名称" prop="title" align="center">
          </el-table-column>
          <el-table-column label="操作" align="center">
            <template slot-scope="{ row }">
              <icon-btn
                icon="plus"
                name="Add the problem"
                @click.native="
                  addProblem(newhomeworkInfo[0].homework_detail.id, row._id)
                "
              ></icon-btn>
            </template>
          </el-table-column>
        </el-table>

        <el-pagination
          class="page"
          layout="prev, pager, next"
          @current-change="getPublicProblem"
          :page-size="limit"
          :total="total"
        >
        </el-pagination>
      </div>
    </el-dialog>
    <!-- 添加题目对话框↑ -->

    <el-dialog title="查重" center :visible.sync="codecheckDialogVisible">
      <el-form
        ref="codecheckrateformref"
        :model="codecheckrateform"
        label-width="80px"
        :rules="rules"
      >
        <el-form-item
          label="查重率(%)"
          prop="codecheckrate"
          label-width="100px"
        >
          <el-input v-model="codecheckrateform.codecheckrate"></el-input>
        </el-form-item>
      </el-form>
      <el-table :data="this.archiveproblemstudentlist" stripe border>
        <el-table-column
          type="index"
          label="序号"
          align="center"
          width="100px"
        ></el-table-column>
        <el-table-column prop="username" label="已完成姓名" align="center">
        </el-table-column>
        <el-table-column label="操作" align="center">
          <template slot-scope="{ row }">
            <el-button
              type="warning"
              round
              @click="
                codecheck(
                  archiveproblemstudentlist,
                  codecheckproblemid,
                  row.id,
                  codecheckhomeworkid,
                  codecheckrateform.codecheckrate
                )
              "
              >查重</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </el-dialog>
    <!-- 查重对话框↑ -->
    <el-dialog
      title="查重详情"
      center
      :visible.sync="codecheckInfoDialogVisible"
    >
      <el-table :data="codecheckinfo" stripe @row-click="show">
        <el-table-column label="展开" type="expand" align="center"
          ><template slot-scope="{ row }">
            <el-row>
              <el-col :span="12">
                <div>
                  提交时间:
                  <span style="margin-left: 4px">{{
                    row.CheckStudentSubTime
                  }}</span>
                </div>
                <div>
                  <pre
                    v-highlight="row.CheckStudentSubmission"
                  ><code></code></pre>
                </div>
              </el-col>
              <el-col :span="12">
                <div>
                  提交时间:
                  <span style="margin-left: 4px">{{
                    row.CompareStudentSubTime
                  }}</span>
                </div>
                <div>
                  <pre
                    v-highlight="row.CompareStudentSubmission"
                  ><code></code></pre>
                </div>
              </el-col>
            </el-row> </template
        ></el-table-column>
        <el-table-column label="被查学号" prop="CheckStudent" align="center">
        </el-table-column>
        <el-table-column label="对比学号" prop="CompareStudent" align="center">
        </el-table-column>
        <el-table-column label="相似率" prop="SimilarityRate" align="center">
        </el-table-column></el-table
    ></el-dialog>
    <!-- 查重详情对话框↑ -->
  </div>
</template>

<script>
import { Home } from "../views";
import api from "../api.js";
export default {
  data() {
    return {
      addProblemDialogVisible: false,
      codecheckDialogVisible: false,
      codecheckInfoDialogVisible: false,
      codecheckproblemid: "",
      codecheckhomeworkid: "",
      codecheckrateform: {
        codecheckrate: "90",
      },

      page: 1,
      limit: 10,
      total: 0,
      loading: false,
      keyword: "",
      problems: [],
      archiveproblemstudentlist: [],
      codecheckinfo: [],
      newhomeworkInfo: this.homeworkInfo,
      rules: {
        codecheckrate: [
          { required: true, message: "请输入查重率", trigger: "blur" },
          {
            pattern: /^(0|[1-9]\d?|100)$/,
            message: "范围在0-100",
            trigger: "blur",
          },
        ],
      },
    };
  },
  props: {
    homeworkInfo: {
      type: Array,
    },
  },
  mounted() {
    this.getPublicProblem();
  },
  methods: {
    getPublicProblem(page) {
      this.loading = true;
      let params = {
        keyword: this.keyword,
        offset: (page - 1) * this.limit,
        limit: this.limit,
      };
      api
        .getProblemList(params)
        .then((res) => {
          this.loading = false;
          this.total = res.data.data.total;
          this.problems = res.data.data.results;
        })
        .catch(() => {});
    },
    addProblem(homeworkid, problemid) {
      api.addProblemtoHw(homeworkid, problemid).then((res) => {
        api
          .getHomework(
            this.newhomeworkInfo[0].homework_detail.group.id,
            this.newhomeworkInfo[0].homework_detail.id
          )
          .then((res) => {
            this.newhomeworkInfo = res.data.data;
          });
      });
    },
    deleteProblem(homeworkid, problemid) {
      api.deleteProblemfromHw(homeworkid, problemid).then((res) => {
        api
          .getHomework(
            this.newhomeworkInfo[0].homework_detail.group.id,
            this.newhomeworkInfo[0].homework_detail.id
          )
          .then((res) => {
            this.newhomeworkInfo = res.data.data;
          });
      });
    },
    opencodecheck(group_id, homework_id, problem_id) {
      api
        .getachieveproblemstudent(group_id, homework_id, problem_id)
        .then((res) => {
          this.codecheckproblemid = problem_id;
          this.codecheckhomeworkid = homework_id;
          this.archiveproblemstudentlist = res.data.data;
          this.codecheckDialogVisible = true;
        });
    },
    codecheck(
      studentlist,
      problem_id,
      check_student_id,
      homework_id,
      codecheckrate
    ) {
      this.$refs.codecheckrateformref.validate((res) => {
        if (!res) {
          return;
        }
        api
          .codecheck(
            studentlist,
            problem_id,
            check_student_id,
            homework_id,
            parseInt(codecheckrate)
          )
          .then((res) => {
            console.log(res);
            this.codecheckinfo = res.data.data;
            this.codecheckInfoDialogVisible = true;
          });
      });
    },
    show(a) {
      console.log(a);
    },
  },
  watch: {
    keyword() {
      this.getPublicProblem(this.page);
    },
  },
};
</script>

<style  scoped>
.homeworkName {
  font-size: 20px;
}
.studentlist {
  width: 300px;
}
.el-container {
  margin: 10px 10px 0 10px;
}
</style>