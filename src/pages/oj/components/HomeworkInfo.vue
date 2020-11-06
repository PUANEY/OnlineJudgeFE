<template>
  <div>
    <el-table
      :data="homeworkinfo[1].problem_list"
      stripe
      @row-click="toproblem"
      :cell-style="cellstyle"
    >
      <el-table-column
        type="index"
        label="序号"
        align="center"
      ></el-table-column>
      <el-table-column label="题目名称" prop="title" align="center">
      </el-table-column>
      <el-table-column label="支持语言" align="center">
        <template slot-scope="{ row }"
          ><span
            v-for="(item, index) in row.languages"
            :key="index"
            style="margin: 0 3px"
            ><el-tag>{{ item }}</el-tag>
          </span>
        </template></el-table-column
      >
    </el-table>
  </div>
</template>

<script>
export default {
  methods: {
    toproblem(row) {
      this.$router.push("/problem/" + row._id);
    },
    cellstyle() {
      return "cursor:pointer";
    },
  },
  props: {
    homeworkinfo: {
      type: Array,
    },
  },
};
</script>

<style  scoped>
</style>