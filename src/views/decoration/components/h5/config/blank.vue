<template>
  <div class="vod-v1-box" v-if="config">
    <div class="title">
      <div class="text">辅助空白块</div>
    </div>

    <div class="config-item">
      <div class="config-item-title">高度</div>
      <div class="config-item-body">
        <div class="d-flex">
          <div>
            <el-input-number v-model="config.height" :min="1"></el-input-number>
          </div>
          <div class="ml-15">
            <span class="helper-text">单位：像素</span>
          </div>
        </div>
      </div>
    </div>

    <div class="config-item">
      <div class="config-item-title">背景颜色</div>
      <div class="config-item-body">
        <el-color-picker v-model="config.bgcolor"></el-color-picker>
      </div>
    </div>

    <div class="float-left mt-15">
      <el-button type="primary" class="w-100" :loading="loading" @click="save">
        保存
      </el-button>
    </div>
  </div>
</template>

<script>
export default {
  props: ["block"],
  data() {
    return {
      config: null,
      loading: false,
    };
  },
  mounted() {
    this.config = this.block.config_render;
  },
  methods: {
    save() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      this.$api.ViewBlock.Update(this.block.id, {
        sort: this.block.sort,
        config: this.config,
      })
        .then(() => {
          this.loading = false;
          this.$message.success(this.$t("common.success"));
          this.$emit("update");
        })
        .catch((e) => {
          this.loading = false;
          this.$message.error(e.message);
        });
    },
  },
};
</script>

<style lang="less" scoped>
.vod-v1-box {
  width: 100%;
  height: auto;
  float: left;
  box-sizing: border-box;

  .title {
    width: 100%;
    height: auto;
    float: left;
    box-sizing: border-box;
    border-left: 5px solid #3ca7fa;
    padding-left: 10px;
    font-size: 16px;
    font-weight: 600;
    color: #333333;
    line-height: 16px;
    margin-bottom: 30px;
  }

  .config-item {
    width: 100%;
    height: auto;
    float: left;
    box-sizing: border-box;
    padding-top: 30px;
    padding-bottom: 30px;
    border-top: 1px solid #f1f2f9;

    .config-item-title {
      width: 100%;
      height: auto;
      float: left;
      box-sizing: border-box;
      font-size: 14px;
      font-weight: 600;
      color: #333333;
      line-height: 14px;
      padding-bottom: 30px;
    }

    .config-item-body {
      width: 100%;
      height: auto;
      float: left;
      box-sizing: border-box;
    }
  }
}
</style>