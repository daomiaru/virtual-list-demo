<template>
    <div ref="itemRef">
            <slot></slot>
    </div>
</template>

<script lang="ts" setup>
type EmitsType = {
    (e: 'mount', val: any) : void
    (e: 'update:height', val: number) : void
}
const itemRef = ref()
const callback = function (mutationsList, observer) {
    console.log('元素改变了')
    emits('mount', itemRef.value, itemRef.value.offsetHeight)
    // emits('update:height', itemRef.value.offsetHeight)
}
const observer = new MutationObserver(callback)

// onMounted(() => {
//     observer.observe(itemRef.value, { attributes: true, childList: true, subtree: true })
// })


const emits = defineEmits<EmitsType>()
onMounted(() => {
    emits('mount', itemRef.value,  itemRef.value.offsetHeigh)
    observer.observe(itemRef.value, { attributes: true, childList: true, subtree: true })
})
onBeforeUnmount(() => {
    observer.disconnect()
})
</script>