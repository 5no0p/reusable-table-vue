<script setup>
import { ref, computed, watch } from 'vue';

const props = defineProps({
    data: Array,
    columns: Array,
    filterKey: String
})

// Define the sort variable
const sortConfig = ref({
  key: null,
  direction: 'asc'
})

// Define debounce and raw filters variables
let rawFilter = computed(() => props.filterKey ?? '')
let debounceFilter = ref('')

let timeoutId = null

watch(rawFilter, (newValue) => {
    clearTimeout(timeoutId)
    timeoutId = setTimeout(() => {
        debounceFilter.value = newValue
    }, 300)
})

// Define the computed property for filtered and sorted data
const filteredData = computed(() => {
  let { data } = props
  const q = debounceFilter.value.toLowerCase()
  
  // Filter the data based on the search query
  if (q) {
    data = data.filter(row =>
      props.columns.some(col => String(row[col] ?? '').toLowerCase().includes(q)
      )
    )
  }

  // Sort the data based on the sort configuration
  const { key, direction } = sortConfig.value
  if (key) {
    const order = direction === 'asc'? 1 : -1
    data = [...data].sort((a, b) => {
      const aValue = a[key]
      const bValue = b[key]

      return (aValue === bValue ? 0 : aValue > bValue ? 1 : -1) * order
    })
  }

  return data
})

</script>

<template>
 <div class="min-w-full mx-60 mt-4">
      <div class="border">
        <table class="min-w-full divide-y">
          <thead>
            <tr>
              <th scope="col" class="px-6 py-3 text-start text-xs font-medium uppercase" @click="sortConfig.key = 'id'; sortConfig.direction = sortConfig.direction === 'asc' ? 'desc' : 'asc'">ID</th>
              <th scope="col" class="px-6 py-3 text-start text-xs font-medium uppercase" @click="sortConfig.key = 'name'; sortConfig.direction = sortConfig.direction === 'asc' ? 'desc' : 'asc'">Name</th>
              <th scope="col" class="px-6 py-3 text-start text-xs font-medium uppercase" @click="sortConfig.key = 'age'; sortConfig.direction = sortConfig.direction === 'asc' ? 'desc' : 'asc'">Age</th>
            </tr>
          </thead>
          <tbody class="divide-y">
            <tr v-for="row in filteredData" :key="row.id" @click="selectedRow = row" :class="{ selected: selectedRow && selectedRow.id === row.id }">
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">{{ row.id }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">{{ row.name }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">{{ row.age }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
</template>

<style scoped></style>