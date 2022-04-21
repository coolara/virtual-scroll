<script setup>
import infiniteList from "./components/HelloWorld.vue";

const songs = Array.from(new Array(100), (_, x) => ({ idx: x, title: x })); // 列表数据
</script>

<template>
  <infinite-list :listData="songs">
    <template #default="{ listItem }">
      <div>{{ listItem.title }}</div>
      <!-- ... -->
    </template>
  </infinite-list>
</template>

<style>
* {
  padding: 0;
  margin: 0;
}
</style>
