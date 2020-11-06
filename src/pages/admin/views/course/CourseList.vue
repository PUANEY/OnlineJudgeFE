<template>
  <div class="class">
    <Panel :title="title">
      <el-button @click="createCourseVisible = true" type="primary" round>{{
        $t("m.Create_Course")
      }}</el-button>
      <el-table
        :data="courseList"
        stripe
        @cell-click="handle"
        :cell-style="cellStyle"
      >
        <el-table-column
          prop="group_name"
          label="课程名称"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="total"
          label="学生人数"
          align="center"
        ></el-table-column>
        <el-table-column
          prop="create_time"
          label="创建时间"
          align="center"
        ></el-table-column>
        </el-table-column>
        <el-table-column label="操作" align="center">
          <template slot-scope="scope">
            <el-tooltip
              class="item"
              effect="dark"
              content="编辑"
              placement="top"
            >
              <el-button
                type="primary"
                icon="el-icon-edit"
                size="mini"
                circle
                @click="editCourseById(scope.row.id)"
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
                @click="removeCourseById(scope.row.id)"
              ></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>
      <el-dialog
        title="创建课程"
        :visible.sync="createCourseVisible"
        width="35%"
        center
        @close="resetcreatedialog"
      >
        <el-form
          label-position="right"
          label-width="80px"
          :model="createCourseForm"
          :rules="rules"
          ref="createCourseForm"
        >
          <el-form-item label="课程名称" prop="group_name">
            <el-input v-model="createCourseForm.group_name"></el-input>
          </el-form-item>
          <el-form-item label="课程密码" prop="entry_code">
            <el-input v-model="createCourseForm.entry_code"></el-input>
          </el-form-item>
        </el-form>
        <div style="text-align: center">
          <el-button type="primary" @click="createCourse">创建</el-button>
        </div>
      </el-dialog>
      <el-dialog
        title="编辑课程"
        :visible.sync="editCourseVisible"
        width="35%"
        center
      >
        <el-form
          label-position="right"
          label-width="80px"
          :model="editCourseForm"
          :rules="editrules"
          ref="editCourseForm"
        >
          <el-form-item label="课程名称" prop="group_name">
            <el-input v-model="editCourseForm.group_name"></el-input>
          </el-form-item>
        </el-form>
        <div style="text-align: center">
          <el-button type="primary" @click="saveCourseInfo">保存</el-button>
        </div>
      </el-dialog>
    </Panel>
  </div>
</template>

<script>
import api from "../../api.js";
export default {
  data() {
    return {
      title: "Course List",
      courseList: [],
      createCourseVisible: false,
      editCourseVisible: false,
      createCourseForm: {
        group_name: "",
        entry_code: "",
      },
      editCourseForm: {},
      rules: {
        group_name: [
          { required: true, message: "请输入课程名称", trigger: "blur" },
        ],
        entry_code: [
          { required: true, message: "请输入课程密码", trigger: "blur" },
          { min: 6, max: 6, message: "请输入6个字符的密码", trigger: "blur" },
        ],
      },
      editrules: {
        group_name: [
          { required: true, message: "请输入课程名称", trigger: "blur" },
        ],
      },
    };
  },
  methods: {
    createCourse() {
      this.$refs.createCourseForm.validate((res) => {
        if (!res) {
          return;
        }
        api.createCourse(this.createCourseForm).then((res) => {
          this.getCourseList();
          this.createCourseVisible = false;
        });
      });
    },
    getCourseList() {
      api.getCourse().then((res) => {
        this.courseList = res.data.data;
      });
    },
    removeCourseById(id) {
      this.$confirm("此操作将永久删除该课程, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          api.deleteCourseById(id).then((res) => {
            this.getCourseList();
            this.createCourseVisible = false;
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
    editCourseById(id) {
      api.getCourse(id).then((res) => {
        this.editCourseForm = res.data.data;
      });
      this.editCourseVisible = true;
    },
    saveCourseInfo() {
      this.$refs.editCourseForm.validate((res) => {
        if (!res) {
        }
        api
          .editCourseById(
            this.editCourseForm.id,
            this.editCourseForm.group_name
          )
          .then((res) => {
            this.getCourseList();
            this.editCourseVisible = false;
          });
      });
    },
    cellStyle() {
      return "cursor:pointer";
    },
    handle(row, cell) {
      if (cell.label == "操作") {
        return;
      }
      this.$router.push("/courseinfo/" + row.id);
    },
    resetcreatedialog() {
      this.createCourseForm = {
        group_name: "",
        entry_code: "",
      };
    },
  },
  mounted() {
    this.getCourseList();
  },
};
</script>

<style  scoped>
</style>