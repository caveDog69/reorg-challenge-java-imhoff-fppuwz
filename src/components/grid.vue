<script setup>
import { ref, computed } from 'vue';
import { EventBus } from './messageBus.js';

const props = defineProps({
  data: Array,
  columns: Array,
  filterKey: String,
});

const sortKey = ref('');
const sortOrders = ref(props.columns.reduce((o, key) => ((o[key] = 1), o), {}));
const selectedDeals = ref([]);

const filteredData = computed(() => {
  let { data, filterKey } = props;
  if (filterKey) {
    filterKey = filterKey.toLowerCase();
    data = data.filter((row) => {
      return Object.keys(row).some((key) => {
        return String(row[key]).toLowerCase().indexOf(filterKey) > -1;
      });
    });
  }
  const key = sortKey.value;
  if (key) {
    const order = sortOrders.value[key];
    data = data.slice().sort((a, b) => {
      a = a[key];
      b = b[key];
      return (a === b ? 0 : a > b ? 1 : -1) * order;
    });
  }
  return data;
});

function sortBy(key) {
  sortKey.value = key;
  sortOrders.value[key] *= -1;
}

function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

function exportToCSV() {
  let csv = createCSV();
  window.open('data:text/csv;charset=utf-8,' + escape(csv));
}

function createCSV() {
  if (filteredData.value.length < 1) {
    return;
  }
  let arrCSV = [];

  for (let i = 0; i < filteredData.value.length; i++) {
    arrCSV.push(Object.values(filteredData.value[i]));
  }

  arrCSV.unshift(Object.keys(filteredData.value[0]));

  return arrCSV.map((line) => line.join(',')).join('\n');
}

function selectRow(deal) {
  let inArray = selectedDeals.value.indexOf(deal);
  if (inArray > -1) {
    selectedDeals.value.splice(inArray, 1);
  } else {
    selectedDeals.value.push(deal);
  }

  EventBus.messageData = {
    selectedDeal: deal || {},
    showPane: selectedDeals.value.length  === 1 ? true : false,
  };
}
</script>

<template>
  <table v-show="filteredData.length">
    <thead>
      <tr>
        <td :colspan="columns.length">
          <button @click="exportToCSV" :disabled="filteredData.length < 1">
            Export to CSV
          </button>
        </td>
      </tr>
      <tr>
        <th
          v-for="key in columns"
          @click="sortBy(key)"
          :class="{ active: sortKey == key }"
        >
          {{ capitalize(key) }}
          <span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'">
          </span>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="entry in filteredData"
        :key="entry.key"
        @click="selectRow(entry)"
        :class="{ active: selectedDeals.indexOf(entry) > -1 }"
      >
        <td v-for="key in columns">
          {{ entry[key] }}
        </td>
      </tr>
    </tbody>
  </table>
  <p v-show="!filteredData.length">No matches found.</p>
</template>

<style>
table {
  border: 2px solid #42b983;
  border-radius: 3px;
  background-color: #fff;
  float: left;
}

th {
  background-color: #42b983;
  color: rgba(255, 255, 255, 0.66);
  cursor: pointer;
  user-select: none;
}

tr {
  background-color: #f9f9f9;
}

tr.active {
  background-color: red;
}


th,
td {
  min-width: 120px;
  padding: 10px 20px;
}

th.active {
  color: #fff;
}

th.active .arrow {
  opacity: 1;
}

.arrow {
  display: inline-block;
  vertical-align: middle;
  width: 0;
  height: 0;
  margin-left: 5px;
  opacity: 0.66;
}

.arrow.asc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-bottom: 4px solid #fff;
}

.arrow.dsc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 4px solid #fff;
}
</style>
