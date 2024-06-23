<template>
    <div ref="itemRef">
        <button @click="handleUpdate">ddd</button>
            <slot></slot>
    </div>
</template>

<script lang="ts" setup>
type EmitsType = {
    (e: 'mount', val: any) : void
    (e: 'sizeChange', val: any) : void
}
type ItemProps = {
    item: any
}
const props = defineProps<ItemProps>()
const itemRef = ref()
const callback = function (mutationsList, observer) {
    console.log('元素改变了')
    emits('mount', itemRef.value, itemRef.value.offsetHeight)
    // emits('update:height', itemRef.value.offsetHeight)
}


// onMounted(() => {
//     observer.observe(itemRef.value, { attributes: true, childList: true, subtree: true })
// })
const resizeObserver:any = null;

const emits = defineEmits<EmitsType>()
const handleUpdate = () => {
    console.log('元素改变了')
    emits("sizeChange", { offsetHeight: Math.floor(Math.random() * 100) + 50, id: props.item.id });
}
onMounted(() => {
    emits('mount', itemRef.value)
    const domNode = itemRef.value;
    const resizeObserver = new ResizeObserver(() => {
        console.log('元素改变了')
        emits("sizeChange", domNode);
    });
    resizeObserver.observe(itemRef.value);
})
onBeforeUnmount(() => {
    if (resizeObserver) {
        resizeObserver?.unobserve(itemRef.value);
    }
})
</script>