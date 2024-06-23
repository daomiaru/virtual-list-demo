<template>
  <div>
      <test2 />
  </div>
</template>


<script lang="ts" setup>
const { ref } = Vue
import test2 from './components/test2.vue'
const msg = ref('hello111')
const count = ref(1)
const toggle = () => {
  msg.value = 'lalalalla'
  count.value++
}
</script>