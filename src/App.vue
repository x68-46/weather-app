<script setup>
import Weather from './components/Weather.vue'
</script>

<template>
  <Weather />
</template>

<style scoped>
:global(body), :global(#app) {
  margin: 0;
  padding: 0;
  min-height: 100vh;
  width: 100vw;
  overflow-x: hidden;
  font-family: 'SF Pro Display', 'Helvetica Neue', Arial, sans-serif;
  background: none;
}
</style>
