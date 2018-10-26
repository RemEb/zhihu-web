<template>
    <div id="content">
        <div id="head">热门内容，来自：社交网络</div>
        <div class="level is-mobile" style="margin-bottom:0px">
            <figure class="image is-32x32 level-left">
                <img :src="contentData.headshot" alt="">
                <strong class="level-left" style="margin-left:10px">{{contentData.name}}</strong>
            </figure>
        </div>

        <Passage :contentData="contentData"></Passage>

        <div class="level is-mobile">
            <div class="level-item tag bottom-background-color">
                <span class="icon">
                    <i class="fas fa-angle-up"></i>
                </span>
                <span class="nav-title">赞同{{formatStarNum}}</span>
            </div>

            <div class="level-item tag bottom-background-color" >
                <span class="icon">
                    <i class="fas fa-angle-down"></i>
                </span>
            </div>

            <div class="level-item">
                <span class="icon">
                    <i class="fas fa-comment-dots"></i>
                </span>
                <span class="levle-item">评论{{formatCommentNum}}</span>
            </div>

            <div class="level-item">
                <span class="icon">
                    <i class="fas fa-star"></i>
                </span>
            </div>

            <div class="level-item">
                <span class="icon">
                    <i class="fas fa-ellipsis-h"></i>
                </span>
            </div>

        </div>

    </div>
</template>

<script>
import Passage from "./Passage.vue";

export default {
    name:"ContentItem",
    props:["contentData"],
    components:{
        Passage,
    },
  computed: {
    formatStarNum: function() {
      if (this.contentData.starNum > 1000) {
        return this.contentData.starNum / 1000 + "k";
      } else {
        return this.contentData.starNum;
      }
    },
    formatCommentNum: function() {
      if (this.contentData.commentNum > 1000) {
        return this.contentData.commentNum / 1000 + "k";
      } else {
        return this.contentData.commentNum;
      }
    }
  }
}
</script>

<style scoped>
#content{
   padding: 10px;
   background-color: white; 
   margin: 0 2px 10px 2px;

}
#head{
    color: lightslategray;
 
}
.bottom-background-color{
    background-color:rgb(230,240,255);
    color:rgb(100,140,210);
    font-size:1em
}
</style>
