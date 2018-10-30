<template>
  <div id="page" @touchstart="touchStart($event)" @touchmove="touchMove($event)" @touchend="touchEnd()">

    <div class="level has-text-centered" v-show="isLoadMoreShow" style="margin:10px 2px 0 2px;background-color:white;">{{loadWords}}</div>

    <item v-for="item in page.data" :key="item.name" :contentData="item"></item>

    <div class="has-text-centered" v-show="loadding">正在加载中状态...</div>

  </div>
</template>

<script>
import Item from "./components/ContentItem.vue";
import Vue from "vue";

export default {
  name: "Page",
  components: {
    Item
  },
  data: function() {
    let data = [];
    for (let i = 0; i < 20; i++) {
      data.push({
        title: "为什么抖音app里帅哥美女那么多？",
        headshot:
          "https://tse1.mm.bing.net/th?id=OIP.7tHmoR_mte6veEPpG4dpRgAAAA&pid=Api&w=350&h=320&rs=1",
        name: "知乎知乎" + i,
        images: [
          "http://p0.qhimgs4.com/t014104515001404918.jpg",
          "http://p0.qhimgs4.com/t014104515001404918.jpg"
        ],
        words:
          "用三张图说明一切，前方高能！！！！！！胆小者、心理承受能力差的就别忘下看了。（抱歉抱歉抱歉抱歉，你们在。。。）",
        starNum: 11100 - i * 100,
        commentNum: 2400 - i * 100
      });
    }

    return {
      data: data,
      top: 0,
      state: 0,
      startY: 0, //保存开始滑动时，y轴位置
      startScroll: 0,
      touching: false,
      isLoadMoreShow: false,
      loadWords: "松开刷新页面",
      num: 0,

      page: {
        totalCount: 0,
        totalPageNum: 0,
        pageSize: 3,
        pageNum: 1,
        data: []
      },
      loadding: false,
      loadMoreing: true
    };
  },
  mounted: function() {
    this.page.totalCount = this.data.length;
    let totalCount = this.page.totalCount;
    let temp = totalCount % this.page.pageSize; //!!
    let pageNum = parseInt(totalCount / this.page.pageSize) + 1;
    if (temp > 0) {
      pageNum++;
    } else if (temp <= 0) {
      pageNum = 1;
    }
    this.page.totalPageNum = pageNum;
    this.getPageData();
    window.addEventListener("scroll", this.scrollFn);
  },

  methods: {
    // 绑定touchstart
    touchStart(e) {
      // 记录下触碰的Y坐标
      this.startY = e.targetTouches[0].pageY;
      // 记录下最开始scrolltop的位置，后面用来判断下拉情况是否应该刷新
      this.startScroll = document.scrollingElement.scrollTop || 0;
      // 任务move都会引起touchMove，增加开关
      this.touching = true;
    },

    // 绑定touchmove
    touchMove(e) {
      // 设置条件，只有当开关为true且起始点击scrolltop为0时才能继续运行函数
      if (!this.touching || document.documentElement.scrollTop > 0) {
        return;
      }

      // 计算移动的距离并判断
      let diff = e.targetTouches[0].pageY - this.startY - this.startScroll;
      if (diff > 0) {
        e.preventDefault();
      }
      this.top = Math.pow(diff, 0.8) + (this.state === 2 ? 40 : 0);

      // 改变顶部div的显隐状态和里面的话
      if (this.top >= 40) {
        this.loadWords = "松开刷新页面";
        this.isLoadMoreShow = true;
      }
    },

    // 绑定touchend
    touchEnd() {
      this.touching = false;
      // 在刷新状态中
      if (this.state === 2) {
        this.state = 2;
        this.top = this.offset;
        return;
      }
      // 执行刷新
      if (this.top >= 40) {
        this.refresh();
      } else {
        this.state = 0;
        this.top = 0;
      }
    },

    // 刷新
    refresh: function() {
      this.loadWords = "刷新成功";
      for (let i = 0 + this.num; i < 3 + this.num; i++) {
        this.data.unshift({
          title: "为什么抖音app里帅哥美女那么多？new",
          headshot:
            "https://tse1.mm.bing.net/th?id=OIP.7tHmoR_mte6veEPpG4dpRgAAAA&pid=Api&w=350&h=320&rs=1",
          name: "知乎知乎 new" + i,
          images: [
            "http://p0.qhimgs4.com/t014104515001404918.jpg",
            "http://p0.qhimgs4.com/t014104515001404918.jpg"
          ],
          words:
            "用三张图说明一切，前方高能！！！！！！胆小者、心理承受能力差的就别忘下看了。（抱歉抱歉抱歉抱歉，你们在。。。）",
          starNum: 11100 - i * 50,
          commentNum: 2400 - i * 50
        });
      }
      this.page.data = [];
      this.page.pageNum = 1;
      this.getPageData();
      this.isLoadMoreShow = false;
      this.num = this.num + 3;
    },

    // 判定翻页动作
    scrollFn: function() {
      if (document.documentElement.scrollTop == 0) return;
      // 真实内容的高度
      let pageHeight = Math.max(
        document.body.scrollHeight,
        document.body.offsetHeight
      );

      // 视窗的高度
      let viewportHeight =
        window.innerHeight ||
        document.documentElement.clientHeight ||
        document.body.clientHeight ||
        0;

      // 隐藏的高度
      let scrollHeight =
        window.pageYOffset ||
        document.documentElement.scrollTop ||
        document.body.scrollHeight ||
        0;

      let pos = pageHeight - viewportHeight - scrollHeight;

      if (pos < 20 && scrollHeight != 0) {
        this.nextPage();
      }
    },

    // 下一页
    nextPage: function() {
      // 判断是否是最后一项
      if (this.page.pageNum === this.page.totalPageNum) {
        this.loadMoreing = false;
        return;
      }
      // 判断是否正在加载数据
      if (this.loadding) {
        return;
      }
      //开启数据
      this.loadding = true;
      this.page.pageNum++;
      let scrollHeight = document.body.scrollHeight;
      setTimeout(() => {
        this.getPageData();
        this.loadding = false;
        Vue.nextTick(function() {
          // DOM 滚动到上次内容最后位置
          document.documentElement.scrollTop = scrollHeight;
        });
      }, 1000);
    },

    // 获得page.data数据
    getPageData: function() {
      // 获得start 和 end 的值
      let start = this.page.pageSize * (this.page.pageNum - 1);
      let end = start + this.page.pageSize;
      if (end > this.page.totalCount) {
        end = this.page.totalCount;
      }
      // page.data增加数据
      const curPageData = this.data.slice(start, end);
      this.page.data = this.page.data.concat(curPageData);
    }
  }
};
</script>

<style scoped>
</style>
