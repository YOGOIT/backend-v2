<template>
  <div class="meedu-main-body" v-loading="loading">
    <back-bar class="mb-30" :title="key"></back-bar>
    <div class="float-left" v-if="config">
      <el-form ref="form" label-width="205px">
        <template v-if="key === '积分'">
          <template v-for="d in config['每日签到']">
            <el-form-item :key="d.id" :label="d.name">
              <div class="d-flex" style="margin-left: 3px">
                <div>
                  <!-- 单行文本输入框 -->
                  <el-input
                    v-if="d.field_type === 'text'"
                    v-model="d.value"
                  ></el-input>
                  <!-- 数字输入框 -->
                  <el-input
                    v-else-if="d.field_type === 'number'"
                    v-model="d.value"
                  ></el-input>
                  <!-- 多行文本输入框 -->
                  <el-input
                    v-else-if="d.field_type === 'textarea'"
                    type="textarea"
                    :rows="3"
                    v-model="d.value"
                  >
                  </el-input>
                  <!-- 富文本输入框 -->
                  <quill-editor
                    :height="400"
                    v-model="d.value"
                    v-else-if="d.field_type === 'longtext'"
                  ></quill-editor>
                  <!-- 图片上传 -->
                  <upload-image
                    v-model="d.value"
                    :name="d.name"
                    v-else-if="d.field_type === 'image'"
                  ></upload-image>

                  <!-- 开关 -->
                  <el-switch
                    v-else-if="d.field_type === 'switch'"
                    v-model="d.value"
                    active-value="1"
                    inactive-value="0"
                  >
                  </el-switch>

                  <!-- 选择 -->
                  <el-select
                    class="w-300px"
                    v-model="d.value"
                    v-else-if="d.field_type === 'select'"
                  >
                    <el-option
                      v-for="(item, index) in d.option_value"
                      :key="index"
                      :label="item.title"
                      :value="item.key"
                    >
                    </el-option>
                  </el-select>
                </div>
                <div class="ml-10" v-if="d.help">
                  <helper-text :text="d.help"></helper-text>
                </div>
              </div>
            </el-form-item>
          </template>
        </template>
        <template v-for="c in config[key]">
          <el-form-item :key="c.id" :label="c.name">
            <div class="d-flex" style="margin-left: 3px">
              <div>
                <!-- 单行文本输入框 -->
                <el-input
                  v-if="c.field_type === 'text'"
                  v-model="c.value"
                ></el-input>
                <!-- 数字输入框 -->
                <el-input
                  v-else-if="c.field_type === 'number'"
                  v-model="c.value"
                ></el-input>
                <!-- 多行文本输入框 -->
                <el-input
                  v-else-if="c.field_type === 'textarea'"
                  type="textarea"
                  :rows="3"
                  v-model="c.value"
                >
                </el-input>
                <!-- 富文本输入框 -->
                <quill-editor
                  :height="400"
                  v-model="c.value"
                  v-else-if="c.field_type === 'longtext'"
                ></quill-editor>
                <!-- 图片上传 -->
                <upload-image
                  v-model="c.value"
                  :name="c.name"
                  v-else-if="c.field_type === 'image'"
                ></upload-image>

                <!-- 开关 -->
                <el-switch
                  v-else-if="c.field_type === 'switch'"
                  v-model="c.value"
                  active-value="1"
                  inactive-value="0"
                >
                </el-switch>

                <!-- 选择 -->
                <el-select
                  class="w-300px"
                  v-model="c.value"
                  v-else-if="c.field_type === 'select'"
                >
                  <el-option
                    v-for="(item, index) in c.option_value"
                    :key="index"
                    :label="item.title"
                    :value="item.key"
                  >
                  </el-option>
                </el-select>
              </div>
              <div class="ml-10" v-if="c.help">
                <helper-text :text="c.help"></helper-text>
              </div>
            </div>
          </el-form-item>
        </template>
      </el-form>
      <div class="bottom-menus">
        <div class="bottom-menus-box">
          <div>
            <el-button @click="save" :loading="loading" type="primary"
              >保存</el-button
            >
          </div>
          <div class="ml-24">
            <el-button @click="$router.back()">取消</el-button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import QuillEditor from "@/components/quill-editor";
import UploadImage from "@/components/upload-image";

export default {
  components: {
    QuillEditor,
    UploadImage,
  },
  data() {
    return {
      key: this.$route.query.key,
      config: null,
      loading: false,
    };
  },
  mounted() {
    this.getConfig();
  },
  methods: {
    getConfig() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      this.config = null;
      this.$api.System.Config.All().then((res) => {
        let configData = res.data;

        // select数据修饰下
        for (let index in configData) {
          for (let index2 in configData[index]) {
            let item = configData[index][index2];

            if (item.field_type === "select") {
              for (let i = 0; i < item.option_value.length; i++) {
                configData[index][index2].option_value[i].key += "";
              }
            }
          }
        }

        this.config = configData;

        setTimeout(() => {
          this.loading = false;
        }, 500);
      });
    },
    save() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      let data = {};
      for (let index in this.config) {
        for (let index2 in this.config[index]) {
          let item = this.config[index][index2];
          data[item.key] = item.value;
        }
      }
      this.$api.System.Config.Save({ config: data }).then(() => {
        this.$message.success(this.$t("common.success"));
        this.loading = false;

        this.getConfig();
      });
    },
  },
};
</script>

<style lang="less" scoped>
.meedu-main-body {
  width: 100%;
}
</style>
