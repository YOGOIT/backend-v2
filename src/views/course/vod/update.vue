<template>
  <div class="meedu-main-body">
    <back-bar class="mb-30" title="编辑录播课程"></back-bar>

    <div class="center-tabs mb-30">
      <div>
        <el-tabs v-model="tab.active">
          <el-tab-pane
            :label="item.name"
            :name="item.key"
            v-for="(item, index) in tab.list"
            :key="index"
          ></el-tab-pane>
        </el-tabs>
      </div>
    </div>

    <div class="float-left" v-if="course">
      <el-form ref="form" :model="course" :rules="rules" label-width="200px">
        <div class="float-left" v-show="tab.active === 'base'">
          <el-form-item prop="category_id" label="所属分类">
            <div class="d-flex">
              <div>
                <el-select v-model="course.category_id">
                  <el-option
                    v-for="(item, index) in categories"
                    :key="index"
                    :label="item.name"
                    :value="item.id"
                  >
                  </el-option>
                </el-select>
              </div>
              <div class="ml-15">
                <p-link
                  text="分类管理"
                  p="courseCategory"
                  type="primary"
                  @click="$router.push({ name: 'CourseCategories' })"
                >
                </p-link>
              </div>
            </div>
          </el-form-item>

          <el-form-item label="课程名" prop="title">
            <el-input v-model="course.title" class="w-500px"></el-input>
          </el-form-item>

          <el-form-item prop="thumb" label="课程封面">
            <upload-image
              v-model="course.thumb"
              helper="长宽比4:3，建议尺寸：400x300像素"
              width="400"
              height="300"
              name="上传封面"
            ></upload-image>
          </el-form-item>

          <el-form-item label="免费" prop="is_free">
            <div class="d-flex">
              <div>
                <el-switch
                  v-model="course.is_free"
                  :active-value="1"
                  :inactive-value="0"
                >
                </el-switch>
              </div>
              <div class="ml-10">
                <helper-text text="用户无需购买即可观看"></helper-text>
              </div>
            </div>
          </el-form-item>

          <el-form-item label="价格" prop="charge" v-if="course.is_free === 0">
            <div class="d-flex">
              <div>
                <el-input
                  type="number"
                  placeholder="单位：元"
                  v-model="course.charge"
                  class="w-200px"
                ></el-input>
              </div>
              <div class="ml-15">
                <helper-text
                  text="价格最小单位为：元，不支持小数。"
                ></helper-text>
              </div>
            </div>
          </el-form-item>

          <el-form-item label="上架时间" prop="published_at">
            <div class="d-flex">
              <div>
                <el-date-picker
                  v-model="course.published_at"
                  type="datetime"
                  format="yyyy-MM-dd HH:mm"
                  value-format="yyyy-MM-dd HH:mm"
                  placeholder="请选择日期"
                >
                </el-date-picker>
              </div>
              <div class="ml-15">
                <div class="helper-text">
                  <helper-text
                    text="上架时间决定课程的排名，时间越早排名越靠后。另外，如果上架时间是未来时间，那么只有时间到了用户才能看到该课程。"
                  ></helper-text>
                </div>
              </div>
            </div>
          </el-form-item>

          <el-form-item label="显示" prop="is_show">
            <div class="d-flex">
              <div>
                <el-switch
                  v-model="course.is_show"
                  :active-value="1"
                  :inactive-value="0"
                >
                </el-switch>
              </div>
              <div class="ml-15">
                <div class="helper-text">
                  <helper-text text="控制用户是否能看到该课程。"></helper-text>
                </div>
              </div>
            </div>
          </el-form-item>

          <el-form-item prop="short_description" label="简短介绍">
            <div class="d-flex">
              <div>
                <el-input
                  class="w-500px"
                  type="textarea"
                  rows="4"
                  maxlength="150"
                  v-model="course.short_description"
                  placeholder="简短介绍"
                ></el-input>
              </div>
              <div class="ml-10">
                <helper-text
                  text="该值会在课程列表显示，建议不要超过150个字。"
                ></helper-text>
              </div>
            </div>
          </el-form-item>

          <el-form-item prop="original_desc" label="详细介绍">
            <quill-editor
              :height="400"
              v-model="course.original_desc"
            ></quill-editor>
          </el-form-item>
        </div>

        <div class="float-left" v-show="tab.active === 'dev'">
          <el-form-item label="推荐" prop="is_rec">
            <div class="d-flex">
              <div>
                <el-switch
                  v-model="course.is_rec"
                  :active-value="1"
                  :inactive-value="0"
                >
                </el-switch>
              </div>
              <div class="ml-15">
                <div class="helper-text">
                  <helper-text text="课程列表过滤使用。"></helper-text>
                </div>
              </div>
            </div>
          </el-form-item>
          <!-- <el-form-item label="订阅人数" prop="user_count">
            <el-input
              v-model="course.user_count"
              class="w-400px"
              placeholder="订阅人数"
            ></el-input>
          </el-form-item> -->
          <el-form-item label="SEO描述">
            <el-input
              class="w-400px"
              type="textarea"
              v-model="course.seo_description"
              placeholder="SEO描述"
            ></el-input>
          </el-form-item>

          <el-form-item label="SEO关键字">
            <el-input
              class="w-400px"
              type="textarea"
              v-model="course.seo_keywords"
              placeholder="SEO关键字"
            ></el-input>
          </el-form-item>
        </div>
      </el-form>

      <div class="bottom-menus">
        <div class="bottom-menus-box">
          <div>
            <el-button @click="formValidate" :loading="loading" type="primary">
              保存
            </el-button>
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
      id: null,
      course: null,
      tab: {
        active: "base",
        list: [
          {
            name: "基础信息",
            key: "base",
          },
          {
            name: "可选信息",
            key: "dev",
          },
        ],
      },
      rules: {
        category_id: [
          {
            required: true,
            message: "分类不能为空",
            trigger: "blur",
          },
        ],
        charge: [
          {
            required: true,
            message: "价格不能为空",
            trigger: "blur",
          },
        ],
        published_at: [
          {
            required: true,
            message: "上架时间不能为空",
            trigger: "blur",
          },
        ],
        is_show: [
          {
            required: true,
            message: "请选择显示",
            trigger: "blur",
          },
        ],
        title: [
          {
            required: true,
            message: "课程名不能为空",
            trigger: "blur",
          },
        ],
        thumb: [
          {
            required: true,
            message: "请上传课程封面",
            trigger: "blur",
          },
        ],
        short_description: [
          {
            required: true,
            message: "简短介绍不能为空",
            trigger: "blur",
          },
        ],
        original_desc: [
          {
            required: true,
            message: "详细介绍不能为空",
            trigger: "blur",
          },
        ],
      },
      comments: [
        {
          id: 0,
          name: "关闭评论",
        },
        {
          id: 1,
          name: "所有人均可评论",
        },
        {
          id: 2,
          name: "订阅后可评论",
        },
      ],
      categories: [],
      loading: false,
    };
  },
  mounted() {
    this.id = this.$route.query.id;

    this.params();
    this.detail();
  },
  methods: {
    detail() {
      this.$api.Course.Vod.Detail(this.id).then((res) => {
        this.course = res.data;
      });
    },
    params() {
      this.$api.Course.Vod.Create().then((res) => {
        this.categories = res.data.categories;
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
      this.course.render_desc = this.course.original_desc;
      if (this.course.is_free === 1) {
        this.course.charge = 0;
      }
      this.$api.Course.Vod.Update(this.course.id, this.course)
        .then(() => {
          this.$message.success(this.$t("common.success"));
          this.$router.push({ name: "Vod" });
        })
        .catch((e) => {
          this.loading = false;
          this.$message.error(e.message);
        });
    },
  },
};
</script>