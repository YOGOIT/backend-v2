<template>
  <div class="meedu-main-body">
    <div class="float-left mb-30">
      <p-button
        text="添加"
        p="addons.learnPaths.path.store"
        @click="$router.push({ name: 'LearningPathCreate' })"
        type="primary"
      >
      </p-button>
    </div>
    <div class="float-left" v-loading="loading">
      <div class="float-left">
        <el-table :data="results" class="float-left">
          <el-table-column prop="id" label="ID" width="120"> </el-table-column>
          <el-table-column prop="name" label="路径名" width="400">
          </el-table-column>
          <el-table-column label="价格">
            <template slot-scope="scope">
              <div>现价：{{ scope.row.charge }}元</div>
              <div style="color: #666" class="original-charge">
                原价：{{ scope.row.original_charge }}元
              </div>
            </template>
          </el-table-column>

          <el-table-column label="步骤" width="200">
            <template slot-scope="scope">
              <span>{{ scope.row.steps_count }}个步骤</span>
            </template>
          </el-table-column>
          <el-table-column label="课程" width="200">
            <template slot-scope="scope">
              <span>{{ scope.row.courses_count }}个课程</span>
            </template>
          </el-table-column>

          <el-table-column fixed="right" label="操作" width="120">
            <template slot-scope="scope">
              <p-link
                text="步骤"
                p="addons.learnPaths.step.list"
                type="primary"
                @click="
                  $router.push({
                    name: 'LearningStep',
                    query: { id: scope.row.id },
                  })
                "
              ></p-link>

              <p-link
                text="编辑"
                p="addons.learnPaths.path.update"
                type="primary"
                class="ml-5"
                @click="
                  $router.push({
                    name: 'LearningPathUpdate',
                    query: { id: scope.row.id },
                  })
                "
              ></p-link>
              <p-link
                text="删除"
                class="ml-5"
                p="addons.learnPaths.path.delete"
                type="danger"
                @click="destory(scope.row.id)"
              ></p-link>
            </template>
          </el-table-column>
        </el-table>
      </div>

      <div class="float-left mt-30 text-center">
        <el-pagination
          @size-change="paginationSizeChange"
          @current-change="paginationPageChange"
          :current-page="pagination.page"
          :page-sizes="[10, 20, 50, 100]"
          :page-size="pagination.size"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pageName: "learnpath-list",
      pagination: {
        page: 1,
        size: 10,
      },
      total: 0,
      loading: false,
      results: [],
    };
  },
  activated() {
    this.getResults();
    this.$utils.scrollTopSet(this.pageName);
  },
  beforeRouteLeave(to, from, next) {
    this.$utils.scrollTopRecord(this.pageName);
    next();
  },
  methods: {
    paginationReset() {
      this.pagination.page = 1;
      this.getResults();
    },
    paginationSizeChange(size) {
      this.pagination.size = size;
      this.getResults();
    },
    paginationPageChange(page) {
      this.pagination.page = page;
      this.getResults();
    },
    getResults() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      let params = {};

      Object.assign(params, this.pagination);
      this.$api.Course.LearnPath.Path.List(params).then((res) => {
        this.loading = false;
        this.results = res.data.data;
        this.total = res.data.total;
      });
    },
    destory(item) {
      this.$confirm("确认操作？", "警告", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          //点击确定按钮的操作
          if (this.loading) {
            return;
          }
          this.loading = true;
          this.$api.Course.LearnPath.Path.Destory(item)
            .then(() => {
              this.loading = false;
              this.$message.success(this.$t("common.success"));
              this.getResults();
            })
            .catch((e) => {
              this.loading = false;
              this.$message.error(e.message);
            });
        })
        .catch(() => {
          //点击删除按钮的操作
        });
    },
  },
};
</script>


<style lang="less" scoped>
.original-charge {
  text-decoration: line-through;
}
</style>