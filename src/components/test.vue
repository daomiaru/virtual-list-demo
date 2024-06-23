<template>
  <div class="home">
    <!-- 滚动区域 -->

    <div
      :style="{
        height: `${scrollHeight}px`,
      }"
      class="scrollContent"
      ref="scrollContentRef"
    >
    <!-- 内容区域 -->
    <div
      class="content"
      :style="{
        position: 'relative',
        height: `${allItemHeight}px`,


      }"
    >
    <div :style="{
        position: 'relative',
...getDynamicPadding
    }">
        <div class="item" v-for="(i, index) in list" :key="i">
        {{ `item${i}` }}
      </div>
    </div>

    </div>
</div>

  </div>
</template>

<script lang="ts" setup>
import { debounce } from "lodash-es";
import { ref, onMounted, onBeforeUnmount } from "vue";
const maxItemNum = 10; //最大显示item数
const allData = ref([])
for(let i = 0; i < 100; i++) {
    allData.value.push(i)
}
const itemHeight = ref(50); //固定item高度
const allItemHeight = computed(() => { //总高度
  return allData.value.length * itemHeight.value;
});

const startIndex = ref(0); //开始索引
const buffefIndex = ref(5) // 缓冲索引 
const endIndex = computed(() => { //结束索引
  return startIndex.value  + maxItemNum;
});

const list = computed(() => { //显示的数据
return allData.value.slice(Math.max(startIndex.value - buffefIndex.value, 0), endIndex.value + buffefIndex.value);
});
const getDynamicPadding = computed(() => {
    return {
        top: `${Math.max((startIndex.value - buffefIndex.value), 0) * itemHeight.value}px`
    }
})
const scrollContentRef = ref()
const scrollHeight = computed(() => { //滚动区域高度
  return 10 * itemHeight.value;
});

const handleScroll = debounce((e) => {
    console.log(e.target.scrollTop)
    startIndex.value = Math.floor(e.target.scrollTop / itemHeight.value)
}, 0);
onMounted(() => {
    scrollContentRef.value.addEventListener('scroll', handleScroll);
}),
onBeforeUnmount(() => {
    scrollContentRef.value.removeEventListener('scroll', handleScroll);
})
</script>

<style>
.scrollContent {
  width: 500px;
  margin: 0 auto;
  box-sizing: border-box;
  border: 1px solid #000;
  overflow-y: auto;
}
.content {
    box-sizing: border-box;
}
.item {
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-bottom: 1px solid #000;
  box-sizing: border-box;
}
</style>
