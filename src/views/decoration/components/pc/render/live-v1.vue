<template>
  <div class="float-left">
    <div class="vod-title">{{ config.title }}</div>
    <div class="vod-courses-box">
      <div class="vod-item" v-for="(item, index) in config.items" :key="index">
        <div class="course-thumb">
          <img v-if="item.thumb" :src="item.thumb" width="100%" />
          <img
            v-else
            src="@/assets/images/decoration/h5/default-live.png"
            width="100%"
          />
        </div>
        <div class="course-title">
          {{ item.title }}
        </div>
        <div class="course-info">
          <div class="videos-count">{{ item.videos_count }}课时</div>
          <div class="charge">
            <span class="unit">￥</span>{{ item.charge }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["config"],
  data() {
    return {};
  },
};
</script>

<style lang="less" scoped>
.vod-title {
  width: 100%;
  height: auto;
  float: left;
  font-size: 30px;
  font-weight: 600;
  color: #333333;
  line-height: 30px;
  text-align: center;
  margin-bottom: 50px;
  margin-top: 50px;
}

.vod-courses-box {
  width: 100%;
  height: auto;
  float: left;
  display: grid;
  gap: 48px;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  margin-bottom: 50px;

  .vod-item {
    background-color: white;
    border-radius: 8px;

    .course-thumb {
      width: 100%;
      height: auto;
      float: left;

      img {
        border-top-left-radius: 8px;
        border-top-right-radius: 8px;
      }
    }

    .course-title {
      width: 100%;
      height: auto;
      float: left;
      box-sizing: border-box;
      padding: 15px 8px;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
      font-size: 16px;
      font-weight: 600;
      color: #333333;
      line-height: 16px;
    }

    .course-info {
      width: 100%;
      height: auto;
      float: left;
      display: flex;
      align-items: center;
      box-sizing: border-box;
      padding: 0 8px 15px 8px;

      .videos-count {
        flex: 1;
        font-size: 14px;
        font-weight: 400;
        color: #666666;
        line-height: 14px;
      }

      .charge {
        font-size: 14px;
        font-weight: 600;
        color: #ef4444;
        line-height: 20px;

        .unit {
          font-size: 14px;
        }
      }
    }
  }
}
</style>