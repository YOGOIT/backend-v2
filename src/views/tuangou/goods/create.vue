<template>
  <div class="meedu-main-body">
    <back-bar class="mb-30" title="添加团购商品"></back-bar>
    <div class="float-left">
      <el-form
        ref="form"
        class="float-left"
        :model="course"
        :rules="rules"
        label-width="200px"
      >
        <el-form-item prop="goods_id" label="商品">
          <el-button type="primary" @click="showSelectResWin = true">
            <span>选择商品</span>
            <span v-if="course.goods_id"
              >已选择「{{ course.goods_title }}」</span
            >
          </el-button>
        </el-form-item>

        <el-form-item label="商品名" prop="goods_title">
          <el-input v-model="course.goods_title" class="w-600px"></el-input>
        </el-form-item>

        <el-form-item label="商品原价" prop="original_charge">
          <el-input
            type="number"
            placeholder="单位：元"
            v-model="course.original_charge"
            class="w-200px"
          ></el-input>
        </el-form-item>

        <el-form-item prop="goods_thumb" label="商品封面">
          <upload-image
            v-model="course.goods_thumb"
            helper="长宽比4:3，建议尺寸：400x300像素"
            width="120"
            height="90"
            name="上传封面"
          ></upload-image>
        </el-form-item>

        <el-form-item prop="desc" label="详细介绍">
          <quill-editor :height="400" v-model="course.desc"></quill-editor>
        </el-form-item>

        <el-form-item label="团购价" prop="charge">
          <div class="d-flex">
            <div>
              <el-input
                type="number"
                v-model="course.charge"
                class="w-200px"
                placeholder="团购价"
              ></el-input>
            </div>
            <div class="ml-10">
              <helper-text text="最小单位：元。不支持小数。"></helper-text>
            </div>
          </div>
        </el-form-item>

        <el-form-item label="组团成功人数" prop="people_num">
          <div class="d-flex">
            <div>
              <el-input
                type="number"
                v-model="course.people_num"
                placeholder="组团成功人数"
                class="w-200px"
              ></el-input>
            </div>
            <div class="ml-10">
              <helper-text
                text="组团达到指定人数即为组团成功。原则上最少为2个人。"
              ></helper-text>
            </div>
          </div>
        </el-form-item>

        <el-form-item label="有效期" prop="time_limit">
          <div class="d-flex">
            <div>
              <el-input
                type="number"
                v-model="course.time_limit"
                placeholder="单位：天"
                class="w-200px"
              ></el-input>
            </div>
            <div class="ml-10">
              <helper-text
                text="团长开团时刻起多少天内有效，失败的话将自动转为退款订单"
              ></helper-text>
            </div>
          </div>
        </el-form-item>

        <el-form-item label="开始时间" prop="started_at">
          <div class="d-flex">
            <div>
              <el-date-picker
                v-model="course.started_at"
                type="datetime"
                format="yyyy-MM-dd hh:mm"
                value-format="yyyy-MM-dd hh:mm"
                placeholder="请选择日期"
                :picker-options="expireTimeOption"
              >
              </el-date-picker>
            </div>
            <div class="mx-10">
              <span class="helper-text">至</span>
            </div>
            <div>
              <el-date-picker
                v-model="course.ended_at"
                type="datetime"
                format="yyyy-MM-dd hh:mm"
                value-format="yyyy-MM-dd hh:mm"
                placeholder="请选择日期"
                :picker-options="expireTimeOption"
              >
              </el-date-picker>
            </div>
            <div class="ml-10">
              <helper-text
                text="团购开始时间，时间达到之后，用户才能参与团购"
              ></helper-text>
            </div>
          </div>
        </el-form-item>

        <el-form-item prop="page_title" label="PC团购页面标题">
          <div class="d-flex">
            <div>
              <el-input
                class="w-500px"
                placeholder="PC团购页面标题"
                v-model="course.page_title"
              ></el-input>
            </div>
            <div class="ml-10">
              <helper-text text="PC打开团购界面浏览器显示的标题"></helper-text>
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

    <select-resource
      v-bind:show="showSelectResWin"
      @change="change"
      @close="showSelectResWin = false"
      enabled-resource="vod,video,live,book,paper,learnPath,practice"
    ></select-resource>
  </div>
</template>
<script>
import QuillEditor from "@/components/quill-editor";
import UploadImage from "@/components/upload-image";
import SelectResource from "@/components/select-resources/index";

export default {
  components: {
    QuillEditor,
    UploadImage,
    SelectResource,
  },
  data() {
    return {
      showSelectResWin: false,
      course: {
        started_at: null,
        ended_at: null,
        goods_title: null,
        other_id: null,
        original_charge: null,
        goods_thumb: null,
        time_limit: null,
        charge: null,
        goods_charge: null,
        goods_id: null,
        people_num: null,
        goods_type: null,
        page_title: null,
        desc: null,
      },
      rules: {
        goods_type: [
          {
            required: true,
            message: "类型不能为空",
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
        goods_title: [
          {
            required: true,
            message: "商品名不能为空",
            trigger: "blur",
          },
        ],
        goods_charge: [
          {
            required: true,
            message: "商品原价不能为空",
            trigger: "blur",
          },
        ],
        goods_id: [
          {
            required: true,
            message: "请选择商品",
            trigger: "blur",
          },
        ],
        time_limit: [
          {
            required: true,
            message: "有效期不能为空",
            trigger: "blur",
          },
        ],
        people_num: [
          {
            required: true,
            message: "人数不能为空",
            trigger: "blur",
          },
        ],
        goods_thumb: [
          {
            required: true,
            message: "请上传商品封面",
            trigger: "blur",
          },
        ],
        started_at: [
          {
            required: true,
            message: "请选择开始时间",
            trigger: "blur",
          },
        ],
        ended_at: [
          {
            required: true,
            message: "请选择开始时间",
            trigger: "blur",
          },
        ],
        desc: [
          {
            required: true,
            message: "详细介绍不能为空",
            trigger: "blur",
          },
        ],
      },
      expireTimeOption: {
        disabledDate(date) {
          return date.getTime() < Date.now();
        },
      },
      loading: false,
    };
  },
  methods: {
    change(data) {
      this.course.goods_id = data.id;
      this.course.other_id = data.id;
      this.course.goods_title = this.course.page_title = data.title;
      this.course.goods_charge = data.original_charge;
      this.course.original_charge = data.original_charge;
      this.course.goods_thumb = data.thumb;

      if (data.resource_type === "vod") {
        this.course.goods_type = "course";
      } else {
        this.course.goods_type = data.resource_type;
      }

      this.showSelectResWin = false;
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
      this.$api.TuanGou.Store(this.course)
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
