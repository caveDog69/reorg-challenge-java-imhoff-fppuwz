<script setup>
import Grid from './components/grid.vue';
import dealData from './data/deals_1.json';
import taskPane from './components/taskPane.vue';
import { ref } from 'vue';

const searchQuery = ref('');
const gridColumns = Object.keys(dealData[0]);
const gridData = dealData;
</script>

<template>
  <form id="search" class="searchForm">
    Filter Deals: <input name="query" v-model="searchQuery" />
  </form>
  <Grid
    :data="gridData"
    :columns="gridColumns"
    :filter-key="searchQuery"
    class="grid"
  ></Grid>
  <taskPane />
</template>

<style>
.searchForm {
  text-align: left;
  margin: 10px;
}
</style>
