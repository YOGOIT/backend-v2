<template>
  <div class="meedu-dialog-mask" v-if="show">
    <div class="meedu-dialog-box">
      <div class="meedu-dialog-header">选择秒杀课程</div>
      <div class="meedu-dialog-body">
        <div class="courses-box">
          <div class="float-left mb-15">
            <div class="float-left d-flex">
              <div class="d-flex">
                <el-input
                  class="w-200px"
                  v-model="pagination.keywords"
                  placeholder="关键字"
                ></el-input>
              </div>

              <div class="ml-15">
                <el-button @click="getCourse" type="primary" plain>
                  筛选
                </el-button>
                <el-button class="ml-15" @click="paginationReset">
                  清空
                </el-button>
              </div>
            </div>
          </div>
          <el-table
            :data="courses"
            highlight-current-row
            @current-change="handleCurrentChange"
            class="float-left mb-15"
            v-loading="loading"
          >
            <el-table-column prop="id" label="ID" width="120">
            </el-table-column>
            <el-table-column label="秒杀课程">
              <template slot-scope="scope">
                <div class="d-flex">
                  <div>
                    <img :src="scope.row.goods_thumb" width="80" height="60" />
                  </div>
                  <div class="ml-15">{{ scope.row.goods_title }}</div>
                </div>
              </template>
            </el-table-column>
            <el-table-column label="秒杀价" width="120">
              <template slot-scope="scope"> ￥{{ scope.row.charge }} </template>
            </el-table-column>
          </el-table>

          <div class="float-left mt-15 text-center">
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
      <div class="meedu-dialog-footer">
        <el-button type="primary" @click="confirm"> 确定 </el-button>
        <el-button @click="close" class="ml-30">取消</el-button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["show"],
  data() {
    return {
      pagination: {
        page: 1,
        size: 10,
        sort: "created_at",
        order: "desc",
        keywords: null,
      },
      loading: false,
      total: 0,
      courses: [],
      result: null,
    };
  },
  mounted() {
    this.getCourse();
  },
  methods: {
    paginationReset() {
      this.pagination.page = 1;
      this.pagination.keywords = null;
      this.getCourse();
    },
    paginationSizeChange(size) {
      this.pagination.size = size;
      this.getCourse();
    },
    paginationPageChange(page) {
      this.pagination.page = page;
      this.getCourse();
    },
    handleCurrentChange(row) {
      this.result = row;
    },
    getCourse() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      this.$api.MiaoSha.List(this.pagination).then((res) => {
        this.loading = false;
        this.courses = res.data.data.data;
        this.total = res.data.data.total;
      });
    },
    confirm() {
      if (this.result === null) {
        this.$message.warning("请选择秒杀课程");
        return;
      }
      this.$emit("change", {
        id: this.result.id,
        goods_id: this.result.goods_id,
        goods_type: this.result.goods_type,
        goods_title: this.result.goods_title,
        goods_thumb: this.result.goods_thumb,
        original_charge: this.result.original_charge,
        num: this.result.num,
        charge: this.result.charge,
        over_num: this.result.over_num,
        started_at: this.result.started_at,
        end_at: this.result.end_at,
        created_at: this.result.created_at,
      });
    },
    close() {
      this.$emit("close");
    },
  },
};
</script>

<style lang="less" scoped>
.courses-box {
  width: 100%;
  height: auto;
  float: left;
}

.meedu-dialog-box {
  width: 900px !important;
  margin-left: -450px !important;
}
</style>