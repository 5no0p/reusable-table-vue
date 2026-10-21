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
      Object.values(row).some(value =>
        String(value).toLowerCase().includes(q.toLowerCase())
      )
    )
  }

  // Sort the data based on the sort configuration
  if (sortConfig.value.key) {
    data = [...data].sort((a, b) => {
      const aValue = a[sortConfig.value.key]
      const bValue = b[sortConfig.value.key]

      if (aValue < bValue) return sortConfig.value.direction === 'asc' ? -1 : 1
      if (aValue > bValue) return sortConfig.value.direction === 'asc' ? 1 : -1
      return 0
    })
  }

  return data
})

</script>

<template>
 <div class="min-w-full">
      <div class="border border-table-line overflow-x-auto [&::-webkit-scrollbar]:h-2 [&::-webkit-scrollbar-thumb]:rounded-none [&::-webkit-scrollbar-track]:bg-scrollbar-track [&::-webkit-scrollbar-thumb]:bg-scrollbar-thumb">
        <table class="min-w-full divide-y divide-table-line">
          <thead>
            <tr>
              <th scope="col" class="px-6 py-3 text-start text-xs font-medium text-muted-foreground-1 uppercase" @click="sortConfig.key = 'id'; sortConfig.direction = sortConfig.direction === 'asc' ? 'desc' : 'asc'">ID</th>
              <th scope="col" class="px-6 py-3 text-start text-xs font-medium text-muted-foreground-1 uppercase" @click="sortConfig.key = 'name'; sortConfig.direction = sortConfig.direction === 'asc' ? 'desc' : 'asc'">Name</th>
              <th scope="col" class="px-6 py-3 text-start text-xs font-medium text-muted-foreground-1 uppercase" @click="sortConfig.key = 'age'; sortConfig.direction = sortConfig.direction === 'asc' ? 'desc' : 'asc'">Age</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-table-line">
            <tr v-for="row in filteredData" :key="row.id" @click="selectedRow = row" :class="{ selected: selectedRow && selectedRow.id === row.id }">
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-foreground">{{ row.id }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-foreground">{{ row.name }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-foreground">{{ row.age }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
</template>

<style scoped></style>