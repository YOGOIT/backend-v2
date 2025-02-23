<template>
  <div class="meedu-main-body">
    <div class="float-left mb-30">
      <p-button
        text="添加用户"
        p="member.store"
        @click="$router.push({ name: 'MemberCreate' })"
        type="primary"
      >
      </p-button>
      <p-button
        text="批量导入"
        p="member.import"
        @click="$router.push({ name: 'MemberImport' })"
        type="primary"
        class="ml-15"
      >
      </p-button>
    </div>
    <div class="float-left">
      <div class="float-left d-flex">
        <div>
          <el-input
            class="w-150px"
            v-model="filter.keywords"
            placeholder="用户列表关键字"
          ></el-input>
        </div>
        <div class="ml-10">
          <el-select
            v-model="filter.role_id"
            class="w-150px"
            placeholder="VIP会员"
            filterable
          >
            <el-option
              v-for="(item, index) in filterData.roles"
              :key="index"
              :label="item.name"
              :value="item.id"
            >
            </el-option>
          </el-select>
        </div>
        <div class="ml-10">
          <el-select
            v-model="filter.tag_id"
            class="w-150px"
            placeholder="用户标签"
            filterable
          >
            <el-option
              v-for="(item, index) in filterData.tags"
              :key="index"
              :label="item.name"
              :value="item.id"
            >
            </el-option>
          </el-select>
        </div>
        <div class="ml-10">
          <el-date-picker
            v-model="filter.created_at"
            type="daterange"
            align="right"
            unlink-panels
            range-separator="至"
            start-placeholder="注册开始日期"
            end-placeholder="注册结束日期"
          >
          </el-date-picker>
        </div>
        <div class="ml-10">
          <el-button @click="getUser" type="primary" plain>筛选</el-button>
          <el-button @click="paginationReset">清空</el-button>
        </div>
      </div>
    </div>
    <div class="float-left mt-30" v-loading="loading">
      <div class="float-left">
        <el-table
          :data="users"
          class="float-left"
          @sort-change="sortChange"
          :default-sort="{ prop: 'id', order: 'descending' }"
        >
          <el-table-column prop="id" sortable label="用户ID" width="100">
          </el-table-column>
          <el-table-column label="用户" width="210">
            <template slot-scope="scope">
              <div class="user-item">
                <div class="avatar">
                  <img :src="scope.row.avatar" width="40" height="40" />
                </div>
                <div class="nickname">{{ scope.row.nick_name }}</div>
              </div>
            </template>
          </el-table-column>
          <el-table-column sortable="" label="注册时间" width="250">
            <template slot-scope="scope">{{
              scope.row.created_at | dateFormat
            }}</template>
          </el-table-column>
          <el-table-column prop="mobile" label="手机号" width="180">
          </el-table-column>
          <el-table-column label="VIP会员" width="150">
            <template slot-scope="scope">
              <span v-if="scope.row.role">{{ scope.row.role.name }}</span>
              <span v-else></span>
            </template>
          </el-table-column>
          <el-table-column prop="credit1" sortable label="积分" width="120">
          </el-table-column>
          <el-table-column label="标签" width="150">
            <template slot-scope="scope">
              <el-tag
                class="ml-5"
                v-for="(item, index) in scope.row.tags"
                :key="index"
              >
                {{ item.name }}
              </el-tag>
            </template>
          </el-table-column>

          <el-table-column label="备注">
            <template slot-scope="scope">
              <div
                v-if="userRemark[scope.row.id]"
                v-html="userRemark[scope.row.id].remark"
              ></div>
            </template>
          </el-table-column>

          <el-table-column fixed="right" label="操作" width="80">
            <template slot-scope="scope">
              <p-link
                text="查看"
                p="member.detail"
                type="primary"
                @click="detail(scope.row)"
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
      pageName: "member-list",
      pagination: {
        page: 1,
        size: 10,
        sort: "id",
        order: "desc",
      },
      filter: {
        keywords: null,
        role_id: null,
        tag_id: null,
        created_at: null,
      },
      total: 0,
      loading: false,
      users: [],
      userRemark: [],
      filterData: {
        tags: [],
        roles: [],
      },
    };
  },
  activated() {
    this.getUser();
    this.$utils.scrollTopSet(this.pageName);
  },
  beforeRouteLeave(to, from, next) {
    this.$utils.scrollTopRecord(this.pageName);
    next();
  },
  methods: {
    paginationReset() {
      this.pagination.page = 1;
      this.filter.keywords = null;
      this.filter.role_id = null;
      this.filter.tag_id = null;
      this.filter.created_at = null;
      this.getUser();
    },
    paginationSizeChange(size) {
      this.pagination.size = size;
      this.getUser();
    },
    paginationPageChange(page) {
      this.pagination.page = page;
      this.getUser();
    },
    sortChange(column) {
      this.pagination.sort = column.prop;
      this.pagination.order = column.order === "ascending" ? "asc" : "desc";
      this.getUser();
    },
    getUser() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      let params = {};
      Object.assign(params, this.filter);
      Object.assign(params, this.pagination);
      this.$api.Member.List(params).then((res) => {
        this.loading = false;
        this.users = res.data.data.data;
        this.total = res.data.data.total;
        // 用户备注
        this.userRemark = res.data.user_remarks;

        this.filterData.tags = res.data.tags;
        this.filterData.roles = res.data.roles;
      });
    },
    detail(item) {
      this.$router.push({ name: "MemberDetail", params: { userId: item.id } });
    },
  },
};
</script>

<style lang="less" scoped>
.user-item {
  width: auto;
  display: flex;
  align-items: center;
  .avatar {
    margin-right: 10px;
  }
  .nickname {
    font-size: 15px;
    font-weight: normal;
  }
}
</style>