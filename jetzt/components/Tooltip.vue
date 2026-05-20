<template>
  <a class="tooltip-wrapper" :href="href" target="_blank" @mouseenter="show = true" @mouseleave="show = false">
    <slot />
    <span v-show="show" class="tooltip-box">{{ text }}</span>
  </a>
</template>

<script setup>
import { ref } from 'vue'
defineProps({ text: String, href: String })
const show = ref(false)
</script>

<style scoped>
.tooltip-wrapper {
  cursor: pointer;
}
.tooltip-box {
  position: fixed;
  top: 15vh;
  left: 15vw;
  width: 40vw;
  /* height: 40vh; */
  overflow: auto;
  background: #1e293b;
  color: #f8fafc;
  padding: 1em 1.5em;
  border-radius: 6px;
  font-size: 1em;
  white-space: normal;
  word-wrap: break-word;
  text-align: left;
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
  z-index: 10;
}
</style>
