<template>
  <div class="meedu-main-body">
    <back-bar class="mb-30" title="编辑课程"></back-bar>
    <div class="float-left" v-if="form">
      <el-form
        ref="form"
        class="float-left"
        :model="form"
        :rules="rules"
        label-width="200px"
      >
        <el-form-item label="课程名" prop="name">
          <el-input
            v-model="form.name"
            class="w-600px"
            placeholder="课程名"
          ></el-input>
        </el-form-item>
        <el-form-item prop="thumb" label="课程封面">
          <upload-image
            v-model="form.thumb"
            helper="长宽比4:3，建议尺寸：400x300像素"
            width="200"
            height="150"
            name="上传封面"
          ></upload-image>
        </el-form-item>
        <el-form-item label="课程价格" prop="charge">
          <div class="d-flex">
            <div>
              <el-input
                type="number"
                placeholder="单位：元"
                v-model="form.charge"
                class="w-200px"
              ></el-input>
            </div>
            <div class="ml-10">
              <helper-text
                text="最小单位：元。不支持小数。该价格仅作为参考，无实际意义。"
              ></helper-text>
            </div>
          </div>
        </el-form-item>
        <el-form-item label="升序" prop="sort">
          <div class="d-flex">
            <div>
              <el-input
                type="number"
                v-model="form.sort"
                class="w-200px"
              ></el-input>
            </div>
            <div class="ml-10">
              <helper-text
                text="请输入整数。小数排在前，大数排在后。"
              ></helper-text>
            </div>
          </div>
        </el-form-item>
      </el-form>

      <div class="bottom-menus">
        <div class="bottom-menus-box">
          <div>
            <el-button @click="formValidate" :loading="loading" type="primary"
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
import UploadImage from "@/components/upload-image";

export default {
  components: {
    UploadImage,
  },
  data() {
    return {
      id: this.$route.query.id,
      form: null,
      categories: [],
      rules: {
        name: [
          {
            required: true,
            message: "请输入课程名",
            trigger: "blur",
          },
        ],
        sort: [
          {
            required: true,
            message: "升序不能为空",
            trigger: "blur",
          },
        ],
        charge: [
          {
            required: true,
            message: "课程价格不能为空",
            trigger: "blur",
          },
        ],
        thumb: [
          {
            required: true,
            message: "课程封面不能为空",
            trigger: "blur",
          },
        ],
      },
      loading: false,
    };
  },
  mounted() {
    this.getDetail();
  },
  methods: {
    getDetail() {
      this.$api.Course.LearnPath.Step.Relation.Detail(this.id).then((res) => {
        this.form = res.data.data;
      });
    },
    formValidate() {
      this.$refs["form"].validate((valid) => {
        if (valid) {
          this.confirm();
        }
      });
    },
    confirm() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      this.$api.Course.LearnPath.Step.Relation.Update(this.id, this.form)
        .then(() => {
          this.$message.success(this.$t("common.success"));
          this.$router.back();
        })
        .catch((e) => {
          this.loading = false;
          this.$message.error(e.message);
        });
    },
  },
};
</script>