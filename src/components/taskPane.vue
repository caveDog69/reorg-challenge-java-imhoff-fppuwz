<script setup>
import { ref, computed } from 'vue';
import { EventBus } from './messageBus.js';

const props = defineProps({
  deal: Array,
  showPane: Boolean,
});

const messageData = computed(() => EventBus.messageData || {});
</script>

<template>
  <div v-show="messageData.showPane" class="pane">
    <ul id="v-for-object" class="rowData">
      <li v-for="(value, name) in messageData.selectedDeal">
        <span class="name">{{ name }}:</span> {{ value }}
      </li>
    </ul>
  </div>
</template>

<style>
.pane {
  display: inline-flex;
  background-color: lightgrey;
  min-width: 200px;
  padding: 10px;
  margin: 5px;
}
.rowData {
  display: block;
  list-style-type: none;
  text-align: left;
}

.name {
  font-weight: bold;
}
</style>
