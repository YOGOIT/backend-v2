<template>
  <div class="meedu-main-body">
    <div class="float-left">
      <div class="float-left mb-30">
        <p-button
          text="添加"
          p="addons.meedu_books.book.store"
          @click="$router.push({ name: 'MeedubookCreate' })"
          type="primary"
        >
        </p-button>
        <p-button
          text="电子书评论"
          p="addons.meedu_books.book.comments.list"
          @click="$router.push({ name: 'MeedubookComment' })"
          type="primary"
        >
        </p-button>
        <p-button
          text="文章评论"
          p="addons.meedu_books.book_article.comments.list"
          @click="$router.push({ name: 'MeedubookArticleComment' })"
          type="primary"
        >
        </p-button>
        <option-bar text="电子书推荐" value="电子书"></option-bar>
      </div>
      <div class="float-left">
        <div class="float-left d-flex">
          <div>
            <el-input
              class="w-200px"
              v-model="filter.key"
              placeholder="电子书关键字"
            ></el-input>
          </div>
          <div class="ml-10">
            <el-select v-model="filter.cid" class="w-200px" placeholder="分类">
              <el-option
                v-for="(item, index) in filterData.categories"
                :key="index"
                :label="item.name"
                :value="item.id"
              >
              </el-option>
            </el-select>
          </div>
          <div class="ml-15">
            <el-button @click="getBook" type="primary" plain>筛选</el-button>
            <el-button @click="paginationReset">清空</el-button>
          </div>
        </div>
      </div>
      <div class="float-left mt-30" v-loading="loading">
        <div class="float-left">
          <el-table
            :data="mbooks"
            @sort-change="sortChange"
            :default-sort="{ prop: 'id', order: 'descending' }"
            class="float-left"
          >
            <el-table-column prop="id" sortable label="ID" width="100">
            </el-table-column>
            <el-table-column prop="category.name" label="分类">
            </el-table-column>
            <el-table-column label="电子书" width="400">
              <template slot-scope="scope">
                <div class="d-flex">
                  <div>
                    <img :src="scope.row.thumb" width="84" height="112" />
                  </div>
                  <div class="ml-10">
                    {{ scope.row.name }}
                  </div>
                </div>
              </template>
            </el-table-column>
            <el-table-column
              label="价格"
              property="charge"
              sortable
              width="100"
            >
              <template slot-scope="scope">
                <span>{{ scope.row.charge }}元</span>
              </template>
            </el-table-column>
            <el-table-column
              label="浏览"
              property="view_times"
              sortable
              width="100"
            >
              <template slot-scope="scope">
                <span>{{ scope.row.view_times }}次</span>
              </template>
            </el-table-column>
            <el-table-column
              label="订阅人数"
              property="user_count"
              sortable
              width="150"
            >
              <template slot-scope="scope">
                <span>{{ scope.row.user_count }}人</span>
              </template>
            </el-table-column>
            <el-table-column label="上架时间" sortable width="200">
              <template slot-scope="scope">{{
                scope.row.published_at | dateFormat
              }}</template>
            </el-table-column>
            <el-table-column fixed="right" label="操作" width="150">
              <template slot-scope="scope">
                <p-link
                  text="文章"
                  p="addons.meedu_books.book_article.list"
                  type="primary"
                  @click="
                    $router.push({
                      name: 'MeedubookArticle',
                      query: { bid: scope.row.id },
                    })
                  "
                ></p-link>
                <p-link
                  text="编辑"
                  class="ml-5"
                  p="addons.meedu_books.book.update"
                  type="primary"
                  @click="
                    $router.push({
                      name: 'MeedubookUpdate',
                      query: { id: scope.row.id },
                    })
                  "
                ></p-link>
                <p-link
                  text="用户"
                  p="addons.meedu_books.book.users"
                  type="primary"
                  class="ml-5"
                  @click="
                    $router.push({
                      name: 'MeedubookUsers',
                      query: { bid: scope.row.id },
                    })
                  "
                ></p-link>

                <p-link
                  text="删除"
                  class="ml-5"
                  type="danger"
                  @click="destory(scope.row.id)"
                  p="addons.meedu_books.book.delete"
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
  </div>
</template>

<script>
export default {
  data() {
    return {
      pageName: "book-list",
      pagination: {
        page: 1,
        size: 10,
        sort: "id",
        order: "desc",
      },
      filter: {
        key: null,
        cid: null,
      },
      total: 0,
      loading: false,
      mbooks: [],
      filterData: {
        categories: [],
      },
    };
  },
  activated() {
    this.getBook();
    this.$utils.scrollTopSet(this.pageName);
  },
  beforeRouteLeave(to, from, next) {
    this.$utils.scrollTopRecord(this.pageName);
    next();
  },
  methods: {
    paginationReset() {
      this.pagination.page = 1;
      this.filter.key = null;
      this.filter.cid = null;
      this.getBook();
    },
    paginationSizeChange(size) {
      this.pagination.size = size;
      this.getBook();
    },
    paginationPageChange(page) {
      this.pagination.page = page;
      this.getBook();
    },
    sortChange(column) {
      this.pagination.sort = column.prop;
      this.pagination.order = column.order === "ascending" ? "asc" : "desc";
      this.getBook();
    },
    getBook() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      let params = {};
      Object.assign(params, this.filter);
      Object.assign(params, this.pagination);
      this.$api.Meedubook.Book.List(params).then((res) => {
        this.loading = false;
        this.mbooks = res.data.data.data;
        this.total = res.data.data.total;
        this.filterData.categories = res.data.categories;
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
          this.$api.Meedubook.Book.Destory(item)
            .then(() => {
              this.loading = false;
              this.$message.success(this.$t("common.success"));
              this.paginationReset();
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
.filter-box {
  width: 100%;
  height: auto;
  float: left;
  box-sizing: border-box;
  padding: 30px;
  border-radius: 15px;
  margin-bottom: 15px;
  background-color: white;

  .filter-label {
    font-size: 14px;
    color: rgba(0, 0, 0, 0.7);
  }
}
</style>
