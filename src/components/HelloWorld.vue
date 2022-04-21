<script setup>
/**
单个条目高度 uh 已知
1. 首先获取容器高度 ch
2. 根据容器高度算出条目activeRows = Math.ceil(ch / 单个条目高度uh)
3. 根据activeRows得出可视区域列表 data = list.slice(startIndex = 0 , endIndex = startIndex + activeRows - 1)
 完成初次渲染
4. startIndex = Math.floor(容器scrollTop/ uh)
5. 定位元素位置 pos =  startIndex * height

 */
import { onMounted, ref, defineProps, computed } from "vue";
const props = defineProps({ listData: Array });

let lists = ref([]);
const uh = 40;
let ch = ref(0);
const activeRows = computed(() => {
  return Math.ceil(ch.value / uh) + 2;
});
let startIndex = ref(0);

const lastIndex = computed(() => {
  const len = props.listData.length;
  const lastIndex = startIndex.value + activeRows.value;
  return lastIndex > len ? len : lastIndex;
});

const difference = (dataSlice, start, last) => {
  const indexes = [];
  console.log(dataSlice, start, last);
  dataSlice.forEach((item, i) => {
    if (item.index < start || item.index > last) {
      indexes.push(i);
    }
  });
  return indexes;
};
const listData = computed(() => {
  let data = lists.value;
  if (!data.length) {
    let index = startIndex.value;
    return (data = props.listData
      .slice(index, lastIndex.value + 1)
      .map((v) => ({
        ...v,
        pos: index * uh,
        index: index++,
      })));
  }
  // const differences = difference(data, startIndex.value, lastIndex.value);
  // console.log("differences", differences);
  // let newIndex = !dr.value
  //   ? lastIndex.value - differences + 1
  //   : startIndex.value;
  // differences.forEach((index) => {
  //   const item = data[index];
  //   item.pos = newIndex * uh;
  //   item.index = newIndex++;
  // });
  // return data;
});
const sh = computed(() => {
  return props.listData.length * uh;
});
let ulRef = ref(null);
let divRef = ref(null);
let lastScrollTop = ref(0);
let dr = ref(false);

onMounted(() => {
  ch.value = divRef.value.offsetHeight;
  divRef.value.onscroll = () => {
    const divScrollTop = divRef.value.scrollTop;
    dr.value = lastScrollTop.value > divScrollTop;
    lastScrollTop.value = divScrollTop;

    startIndex.value = Math.floor(lastScrollTop.value / uh);
  };
});
</script>

<template>
  <div class="div" ref="divRef">
    <ul ref="ulRef" class="ul" :style="{ height: `${sh}px` }">
      <li
        v-for="(listItem, listIndex) in listData"
        :style="{
          position: 'absolute',
          transform: `translateY(${listItem.pos}px)`,
        }"
        :key="`list-${listIndex}`"
      >
        <slot :listItem="listItem"></slot>
      </li>
    </ul>
  </div>
</template>
<style>
.div {
  background: lightyellow;
  height: 200px;
  overflow: auto;
}
.ul {
  position: relative;
}
</style>
