<template>
    <div>
        <strong>{{contentData.title}}</strong>
        <div class="level  is-mobile">
            <div class="level-item"><img :src="contentData.images[0]" class="image is-128x128"></div>
            <div class="level-item"> <img :src="contentData.images[1]" class="image is-128x128"></div>
        </div>
        <div class="text-hidden" style="height:50px">
            {{contentData.words}}
        </div>

    </div>
</template>

<script>
export default {
  props: ["contentData"],
  name: "Passage"
};
</script>

<style scoped>
.text-hidden {
  text-overflow: -o-ellipsis-lastline;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}
strong {
  font-size: 1.2em;
}
</style>
