<template>
  <div class="meedu-main-body">
    <back-bar class="mb-30" title="直播排课"></back-bar>
    <div class="float-left mb-30">
      <p-button
        text="添加"
        @click="
          $router.push({
            name: 'LiveCourseVideoCreate',
            query: { course_id: pagination.course_id },
          })
        "
        type="primary"
        p="addons.Zhibo.course_video.store"
      >
      </p-button>
    </div>
    <div class="float-left" v-loading="loading">
      <div class="float-left">
        <el-table :data="results" class="float-left">
          <el-table-column prop="id" label="ID" width="120"> </el-table-column>
          <el-table-column prop="name" label="标题">
            <template slot-scope="scope">
              <span>{{ scope.row.chapter.name }}</span>
              <span class="mx-5">/</span>
              <span>{{ scope.row.title }}</span>
            </template>
          </el-table-column>
          <el-table-column label="直播时间" width="200">
            <template slot-scope="scope">{{
              scope.row.published_at | dateFormat
            }}</template>
          </el-table-column>
          <el-table-column label="状态" width="100">
            <template slot-scope="scope">
              <el-tag type="success" v-if="scope.row.status == 1">
                {{ scope.row.status_text }}
              </el-tag>
              <el-tag v-else-if="scope.row.status == 2">
                {{ scope.row.status_text }}
              </el-tag>
              <el-tag v-else type="info">
                {{ scope.row.status_text }}
              </el-tag>
            </template>
          </el-table-column>
          <el-table-column fixed="right" label="操作" width="220">
            <template slot-scope="scope">
              <p-link
                v-if="scope.row.status == 1"
                text="继续直播"
                type="primary"
                @click="
                  $router.push({
                    name: 'LiveCourseVideoPlay',
                    query: {
                      video_id: scope.row.id,
                      course_id: scope.row.course_id,
                    },
                  })
                "
                p="addons.Zhibo.zhibo.open"
              ></p-link>
              <p-link
                v-else
                text="开播"
                type="primary"
                @click="
                  $router.push({
                    name: 'LiveCourseVideoPlay',
                    query: {
                      video_id: scope.row.id,
                      course_id: scope.row.course_id,
                    },
                  })
                "
                p="addons.Zhibo.zhibo.open"
              ></p-link>
              <p-link
                text="观看"
                type="primary"
                class="ml-5"
                @click="
                  $router.push({
                    name: 'LiveCourseVideoWatchusers',
                    query: {
                      id: scope.row.id,
                      course_id: scope.row.course_id,
                    },
                  })
                "
                p="addons.Zhibo.course_video.watch.users"
              ></p-link>
              <p-link
                text="讨论"
                type="primary"
                class="ml-5"
                @click="
                  $router.push({
                    name: 'LiveCourseVideoChat',
                    query: {
                      id: scope.row.id,
                      course_id: scope.row.course_id,
                    },
                  })
                "
                p="addons.Zhibo.chat.list"
              ></p-link>

              <p-link
                text="编辑"
                type="primary"
                class="ml-5"
                @click="
                  $router.push({
                    name: 'LiveCourseVideoUpdate',
                    query: {
                      id: scope.row.id,
                      course_id: scope.row.course_id,
                    },
                  })
                "
                p="addons.Zhibo.course_video.update"
              >
              </p-link>
              <p-link
                text="删除"
                class="ml-5"
                type="danger"
                @click="destory(scope.row.id)"
                p="addons.Zhibo.course_video.delete"
              >
              </p-link>
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
      pageName: "liveVideo-list",
      pagination: {
        course_id: this.$route.query.id,
        keywords: "",
        page: 1,
        size: 10,
      },
      total: 0,
      loading: false,
      results: [],
    };
  },
  watch: {
    "$route.query.id"() {
      this.pagination.page = 1;
      this.pagination.keywords = "";
    },
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
      this.pagination.keywords = "";
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
      this.pagination.course_id = this.$route.query.id;
      Object.assign(params, this.pagination);
      this.$api.Course.Live.Course.Video.List(params).then((res) => {
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
          this.$api.Course.Live.Course.Video.Destory(item)
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
