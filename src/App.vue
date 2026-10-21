<script setup>
import { ref, computed, watch } from 'vue'
import Grid from './Grid.vue'

// A reusable data table application that will have search, sort, paginate and select rows
// Practice: Props, emits, reusable components

// Define the table data
const tableData = ref([
  { id: 1, name: 'John Doe', age: 30 },
  { id: 2, name: 'Jane Smith', age: 25 },
  { id: 3, name: 'Bob Johnson', age: 40 },
])

const tableColumns = ['id', 'name', 'age']

// Define the selected row
const selectedRow = ref(null)

// Define the search query variable
const searchQuery = ref('')


</script>

<template>
  <div class="container flex flex-col items-center justify-center min-h-screen">
    <h1 class="text-2xl font-bold mb-4">Reusable Data Table</h1>
    <div class="my-2 mb-4">
      <input class="border border-gray-400 rounded px-2 focus:outline-none focus:ring-2 focus:ring-blue-500" type="text" v-model="searchQuery" placeholder="Search..." />
    </div>
      <Grid 
        :data="tableData"
        :columns="tableColumns"
        :filter-key="searchQuery"
      />
  </div>
</template>

<style scoped></style>
