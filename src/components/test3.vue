<template>
    <!-- <Item></Item>     -->
     <!-- :ref="(el) => renderItemsRef(el, item.id)" -->
    <button @click="handleTest">插入动态元素</button>
    <div ref="viewportRef" class="viewport" @scroll="handleScroll">
        <div class="content-placeholder" :style="{ height: `${renderTotalHeight}px` }">
            <div class="content-wrap" :style="{ transform: `translateY(${offset}px)` }">
                <Item v-for="item in renderItems"  :key="item.id" @mount="(node: any) => {
                     renderItemsRef(node, item.id, height)
                }"
                    :style="{ height: `${item.height}px` }" class="content-item">
                    {{ item.text }}
                </Item>
            </div>
        </div>
    </div>
</template>

<script setup>
import Item from './item.vue';
import { ref, onMounted, nextTick } from 'vue';
const RENDER_SIZE = 15; // 假设每次渲染 15 条数据
const ITEM_HEIGHT = 100; // 假设每个 item 真实渲染后高度接近 100px
// 假设 10000 条数据
const allItems = Array.from({ length: 20 }, (v, i) => {
    return {
        id: i,
        text: `item-${i + 1}`,
        // 设置随机高度, 在实际项目中应该根据 item 的实际情况设置一个接近于 item 渲染后的高度
        height: Math.floor(Math.random() * 100) + 50
    };
});

const viewportRef = ref(null);
const renderItems = ref([]); // 当前需要渲染的 item
const renderTotalHeight = ref(0); // 整个已渲染列表的高度
const hasRenderedItemsHeight = ref({}); // 已渲染的 item 数据 height 
const offset = ref(0);
const handleTest = () => {
    allItems.splice(10, 0, {
        id: 99,
        text: `item-99`,
        height: Math.floor(Math.random() * 100) + 50
    })
}
const updateRenderItems = () => {
    const scrollTop = viewportRef.value?.scrollTop;

    let startIndex = 0;
    let startOffset = 0;

    for (let i = 0; i < allItems.length; i++) {
        const h = hasRenderedItemsHeight.value[allItems[i].id] || ITEM_HEIGHT;
        startOffset += h;
        if (startOffset >= scrollTop) {
            startIndex = i;
            break;
        }
    }

    renderItems.value = allItems.slice(startIndex, startIndex + RENDER_SIZE);
    offset.value = startOffset - hasRenderedItemsHeight.value[allItems[startIndex].id];
    // offset.value = startOffset

}

const renderItemsRef = (el, id) => {
    if (el) {
        // 存放已渲染的 item 的高度
        console.log(el.offsetHeight)
        hasRenderedItemsHeight.value[id] = el.offsetHeight;
        // console.log(hasRenderedItemsHeight)
        // 更新容器的高度
        nextTick(updateRenderTotalHeight);
    }
}

const updateRenderTotalHeight = () => {
    renderTotalHeight.value = allItems.reduce((sum, item) => sum + (hasRenderedItemsHeight.value[item.id] || ITEM_HEIGHT), 0);
    console.log(renderTotalHeight.value)
}

const handleScroll = () => {
    updateRenderItems();
}

onMounted(() => {
    updateRenderItems();
})
</script>

<style>
.viewport {
    height: 800px;
    overflow-y: auto;
    position: relative;
}

.content-placeholder {
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
}

.content-item {
    width: 100%;
    border: 1px solid #000;
    display: flex;
    justify-content: center;
    align-items: center;
}
</style>