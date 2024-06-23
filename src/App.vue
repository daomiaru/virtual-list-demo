<template>
  <div>
      <test3 />
  </div>
</template>


<script lang="ts" setup>
const { ref } = Vue
import test3 from './components/test3.vue'
const msg = ref('hello111')
const count = ref(1)
const toggle = () => {
  msg.value = 'lalalalla'
  count.value++
}
</script>