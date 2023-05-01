<script setup lang="ts">
import { ref } from 'vue'

defineProps(

  {
    type:{
default:0
    },
    progress:{
    default:0
  },aborting:{
    default:0
  },opacity:{
    default:0
  }}
)

</script>

<template>
  <div class="block">
    <div :class="'type'+type" :style="'height:'+(100-progress)+'px; opacity: '+(aborting?opacity:1)">
    </div>
  </div>
</template>

<style scoped>
.block {
  display: flex;
  width: 50px;
  height: 100px;
  border: 5px solid #FFF;
  margin: 5px;
  align-items: center;
 
}
.block>div{
  width: 100%;
  /* transition-property: opacity;
  transition-duration: 500ms; */
}
.type0 {
  background-color: lightgreen;
}
.type1 {
  background-color: lightblue;
}
.type2 {
  background-color: lightcoral;
}
</style>
