<template>
  <div class="meedu-main-body">
    <div class="float-left mb-30">
      <p-button
        text="添加"
        p="addons.Paper.mock_paper.store"
        @click="$router.push({ name: 'ExamMockpaperCreate' })"
        type="primary"
      >
      </p-button>
    </div>
    <div class="float-left">
      <div class="float-left d-flex">
        <div>
          <el-input
            v-model="filter.keywords"
            class="w-200px"
            placeholder="模拟试卷关键字"
          ></el-input>
        </div>

        <div class="ml-10">
          <el-select
            class="w-200px"
            placeholder="分类"
            v-model="filter.category_id"
          >
            <el-option
              v-for="(item, index) in filterData.categories"
              :key="index"
              :label="item.name"
              :value="item.id"
            >
            </el-option>
          </el-select>
        </div>

        <div class="ml-10">
          <el-button @click="firstPageLoad()" type="primary" plain>
            筛选
          </el-button>
          <el-button @click="paginationReset()">清空</el-button>
        </div>
      </div>
    </div>
    <div class="float-left mt-30" v-loading="loading">
      <div class="float-left">
        <el-table
          :data="list"
          @sort-change="sortChange"
          :default-sort="{ prop: 'id', order: 'descending' }"
          class="float-left"
        >
          <el-table-column prop="id" label="ID" width="120"> </el-table-column>
          <el-table-column prop="category.name" label="分类" width="200">
          </el-table-column>
          <el-table-column prop="title" label="标题" width="500">
          </el-table-column>
          <el-table-column label="分数" width="150">
            <template slot-scope="scope">
              <div>总分：{{ scope.row.score }}分</div>
              <div class="c-red">及格：{{ scope.row.pass_score }}分</div>
            </template>
          </el-table-column>
          <el-table-column label="时长">
            <template slot-scope="scope">
              <span>{{ scope.row.expired_minutes }}分钟</span>
            </template>
          </el-table-column>
          <el-table-column fixed="right" label="操作" width="220">
            <template slot-scope="scope">
              <p-link
                text="编辑"
                p="addons.Paper.mock_paper.update"
                type="primary"
                @click="
                  $router.push({
                    name: 'ExamMockpaperUpdate',
                    query: { id: scope.row.id },
                  })
                "
              ></p-link>
              <p-link
                text="用户"
                class="ml-5"
                p="addons.Paper.mock_paper.users"
                type="primary"
                @click="
                  $router.push({
                    name: 'MockpaperUser',
                    query: { id: scope.row.id },
                  })
                "
              ></p-link>
              <p-link
                text="统计"
                p="addons.Paper.mock_paper.statistics"
                type="primary"
                class="ml-5"
                @click="
                  $router.push({
                    name: 'ExamMockpaperStat',
                    query: { id: scope.row.id },
                  })
                "
              ></p-link>
              <p-link
                text="考试记录"
                p="addons.Paper.mock_paper.records"
                type="primary"
                class="ml-5"
                @click="
                  $router.push({
                    name: 'ExamMockpaperUserpaper',
                    query: { id: scope.row.id },
                  })
                "
              ></p-link>

              <p-link
                text="删除"
                class="ml-5"
                p="addons.Paper.mock_paper.delete"
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
      pageName: "mockpaper-list",
      pagination: {
        page: 1,
        size: 10,
        sort: "id",
        order: "desc",
      },
      filter: {
        keywords: null,
        category_id: null,
      },
      total: 0,
      loading: false,
      list: [],
      filterData: {
        categories: [],
      },
    };
  },
  activated() {
    this.params();
    this.getResults();
    this.$utils.scrollTopSet(this.pageName);
  },
  beforeRouteLeave(to, from, next) {
    this.$utils.scrollTopRecord(this.pageName);
    next();
  },
  methods: {
    params() {
      this.$api.Exam.Mockpaper.Create().then((res) => {
        this.filterData.categories = res.data.categories;
      });
    },
    firstPageLoad() {
      this.pagination.page = 1;
      this.getResults();
    },
    paginationReset() {
      this.pagination.page = 1;
      this.filter.keywords = null;
      this.filter.category_id = null;
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
    sortChange(column) {
      this.pagination.sort = column.prop;
      this.pagination.order = column.order === "ascending" ? "asc" : "desc";
      this.getResults();
    },
    getResults() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      let params = {};
      Object.assign(params, this.filter, this.pagination);
      this.$api.Exam.Mockpaper.List(params).then((res) => {
        this.loading = false;
        this.list = res.data.data;
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
          this.$api.Exam.Mockpaper.Destory(item)
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
