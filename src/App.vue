<script setup>
import Board from './components/Board.vue';
</script>

<template>
  <div class="gradient-background">
    <Board />
  </div>
</template>

<style>
body {
  background: linear-gradient(to right, rgba(20, 25, 30, 0.8), rgba(30, 25, 20, 0.8));
  /* height: 100vh; */
}
</style>
