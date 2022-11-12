<template>
  <view>
    <scroll-view :style="'width: 100%; height: '+ (windowHeight) +'px;padding-bottom: 30upx '" :scroll-y="true"
                 @scrolltolower="scrolltolower" :lower-threshold="lowerThreshold">
      <view class="my-parent">
        <block v-for="(list,index) in dataList">
          <!-- <view style="width: 32.5%; height: 350upx; background-color: #000000; margin-top: 5upx; margin-left: 5upx;"> -->
          <view class="my-item">
            <image class="my-image" @click="towxh5Video(index)" :src="list.src+'?x-oss-process=video/snapshot,t_1000,f_jpg'"
                   mode="aspectFill" style="width: 100%; height: 100%;"></image>
            <view class="my-top">
              <view class="my-item1">
                <image style="height: 27upx;width: 27upx" src="@/static/font/play.png"></image>
                <text>完整回放</text>
              </view>
              <view class="my-item2">
                <image style="height: 27upx;width: 27upx" src="@/static/font/share.png"></image>
              </view>
            </view>
            <view class="my-bottom">
              广东省老人LOL比赛直播，特约嘉宾李鲁媚
            </view>
          </view>
        </block>
      </view>
    </scroll-view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      windowWidth: 0,
      windowHeight: 0,
      dataList: [],
      lowerThreshold: 0,
      page: 1
    }
  },
  onLoad() {
    //
    this.windowWidth = uni.getSystemInfoSync().windowWidth
    if (uni.getSystemInfoSync().platform == 'ios') {
      var model = uni.getSystemInfoSync().model
      if (model !== 'iPhone6' || model !== 'iPhone6s' || model !== 'iPhone7' || model !== 'iPhone8') {
        // this.windowHeight = (uni.getSystemInfoSync().screenHeight) - 125
        this.windowHeight = (uni.getSystemInfoSync().screenHeight);
      } else {
        this.windowHeight = uni.getSystemInfoSync().screenHeight
      }
    } else {
      this.windowHeight = uni.getSystemInfoSync().screenHeight
    }
    this.get()
    console.log(this.windowHeight)
  },
  methods: {
    towxh5Video(index) {
      let list = []
      let videoNumber = 36;//（请输入 6 的倍数，来控制 DOM 节点的总视频数量）
      let dotNumber = videoNumber / 2;
      let inf = (index + 1) - dotNumber;
      console.log('点击了第' + (index + 1) + '个视频.');
      let beforeLocation = 0;
      let afterLocation = 0;
      let location = 0;
      if (inf <= 0) {
        beforeLocation = 0;
        location = index;
        for (let i = 0; i <= index; i++) {
          list.push(this.dataList[i]);
        }
      } else {
        beforeLocation = inf;
        location = index - inf;
        for (let i = inf; i <= index; i++) {
          list.push(this.dataList[i]);
        }
      }
      let onf = this.dataList.length - (index + 1)
      console.log('下方视频数：' + onf);
      if (onf <= dotNumber) {
        if (onf !== 0) {
          afterLocation = this.dataList.length - 1;
          for (let i = index + 1; i < this.dataList.length; i++) {
            list.push(this.dataList[i]);
          }
        } else {
          afterLocation = this.dataList.length - 1;
        }
      } else {
        afterLocation = index + dotNumber;
        for (let i = index + 1; i <= index + dotNumber; i++) {
          list.push(this.dataList[i]);
        }
      }
      console.log('总视频数：' + list.length);
      console.log('\n\n第一个视频下标：' + beforeLocation + '.\n' + '最后一个视频下标：' + afterLocation + '.\n\n');
      uni.setStorageSync("List", this.dataList);
      uni.setStorageSync("dataList", list);
      uni.navigateTo({
        url: './wxh5infoVideo/wxh5infoVideo?option=' + location + '&page=' + this.page + '&index=' + index,
      })
    },
    scrolltolower() {
      // 这里就是数据加载完以后再向后端发送数据的地方
      this.page++;
      uni.request({
        url: `http://ylgy.qfxwl.com/api/Advassets/pageList?page=${this.page}&page_size=15`,
        // url: 'https://bdb24c6d-8c19-4f80-8e7e-c9c9f037f131.bspapp.com/video',
        method: 'GET',
        data: {
          info: 'get_video'
        },
        success: (res) => {
          var msg = res.data.data
          for (let i = 0; i < msg.length; i++) {
            this.dataList.push(msg[i])
          }
        }
      })
    },
    get() {
      this.page = 1;
      uni.request({
        url: 'http://ylgy.qfxwl.com/api/Advassets/pageList?page=1&page_size=15',
        // url: 'https://bdb24c6d-8c19-4f80-8e7e-c9c9f037f131.bspapp.com/video',
        // method: 'POST',
        method: 'GET',
        success: (res) => {
          var msg = res.data.data
          for (let msgElement of msg) {
            // todo
            msgElement._id = msgElement.id;
            msgElement.username = '阿水的哈';
            msgElement.href = msgElement.cover;
            msgElement.title = 'undefined title';
            msgElement.msg = 'undefined msg';
            msgElement.state = 'pause';
            msgElement.like = 0;
            msgElement.like_n = 0;
            msgElement.sms_n = 0;
            msgElement.src = msgElement.url;
            msgElement.playNumber = 0;
            msgElement.playIng = false;
            msgElement.isShowimage = false;
            msgElement.isShowProgressBarTime = false;
            msgElement.true = false;
            msgElement.abcdefghijklmn67891 = 'undefined userid';
          }
          // console.log(msg)
          this.dataList = msg;
        }
      })
    }
  }
}
</script>

<style>
.test{
  font-family: iconfont;
}


.my-parent {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
}


.my-parent .my-item {
  width: 45%;
  /*height: 350upx;*/
  /* height: 100%; */
  background-color: #000000;
  margin-top: 30upx;
  margin-left: 10upx;
  padding-bottom: 80%;
  /*width: 100%;*/
  position: relative;
  border-radius: 12upx;
}

.my-parent .my-item .my-image{
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}


.my-parent .my-item .my-top{
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  color: white;
  display: flex;
  justify-content: space-between;
  padding: 20upx;
}

.my-parent .my-item .my-top .my-item1{
  background-color: rgba(66, 63, 63, 0.8);
  height: 42upx;
  display: flex;
  font-size: 20upx;
  align-items: center;
  padding: 2upx 10upx;
  border-radius: 18upx;
}

.my-parent .my-item .my-top .my-item2{
  background-color: rgba(66, 63, 63, 0.8);
  height: 42upx;
  display: flex;
  font-size: 20upx;
  align-items: center;
  padding: 5upx 10upx;
  border-radius: 50%;
  color: white;
}




.my-parent .my-item .my-bottom{
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(66, 63, 63,0.2);
  color: white;
  padding: 10upx;
}



</style>