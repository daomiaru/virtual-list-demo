<template>
    <div class="virtual-list" ref="listContainer" @scroll="onScroll">
      <div class="spacer" :style="{ height: `${totalHeight}px` }"></div>
      <div
        class="item"
        v-for="item in visibleItems"
        :key="item.index"
        :style="{
          position: 'absolute',
          top: `${item.top}px`,
          left: 0,
          right: 0,
        }"
        ref="setItemRefs"
      >
        <slot :item="item.data" />
      </div>
    </div>
  </template>
  
  <script>
  import { ref, computed, onMounted, watch, nextTick } from 'vue';
  
  export default {
    name: 'VirtualList',
    props: {
      items: {
        type: Array,
        required: true,
      },
      bufferSize: {
        type: Number,
        default: 5,
      },
    },
    setup(props) {
      const listContainer = ref(null);
      const itemRefs = ref([]);
      const scrollTop = ref(0);
      const itemHeights = ref([]);
      const totalHeight = ref(0);
  
      const visibleItems = computed(() => {
        const startIndex = Math.max(
          Math.floor(scrollTop.value / averageHeight.value) - props.bufferSize,
          0
        );
        const endIndex = Math.min(
          startIndex + Math.ceil(listContainer.value?.clientHeight / averageHeight.value) + props.bufferSize * 2,
          props.items.length
        );
  
        const items = [];
        let top = 0;
  
        for (let i = 0; i < startIndex; i++) {
          top += itemHeights.value[i] || averageHeight.value;
        }
  
        for (let i = startIndex; i < endIndex; i++) {
          const height = itemHeights.value[i] || averageHeight.value;
          items.push({
            data: props.items[i],
            index: i,
            top,
            height,
          });
          top += height;
        }
  
        return items;
      });
  
      const onScroll = () => {
        scrollTop.value = listContainer.value.scrollTop;
      };
  
      const calculateHeights = () => {
        nextTick(() => {
          itemHeights.value = itemRefs.value.map(item => item?.offsetHeight || averageHeight.value);
          totalHeight.value = itemHeights.value.reduce((sum, height) => sum + height, 0);
        });
      };
  
      const setItemRefs = el => {
        if (el) {
          itemRefs.value.push(el);
        }
      };
  
      onMounted(() => {
        calculateHeights();
        const observer = new ResizeObserver(calculateHeights);
        itemRefs.value.forEach(el => el && observer.observe(el));
      });
  
      watch(() => props.items, () => {
        itemRefs.value = [];
        nextTick(calculateHeights);
      });
  
      const averageHeight = computed(() => {
        if (itemHeights.value.length) {
          return itemHeights.value.reduce((sum, height) => sum + height, 0) / itemHeights.value.length;
        } else {
          return 100; // 默认平均高度
        }
      });
  
      return {
        listContainer,
        setItemRefs,
        scrollTop,
        visibleItems,
        totalHeight,
        onScroll,
      };
    },
  };
  </script>
  
  <style>
  .virtual-list {
    position: relative;
    overflow-y: auto;
    width: 100%;
    height: 100%;
  }
  
  .spacer {
    width: 100%;
  }
  
  .item {
    position: absolute;
    width: 100%;
  }
  </style>
  