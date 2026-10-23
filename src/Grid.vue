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

// Define function for sort in the template
function sortBy(sortKey){
  const { key, direction } = sortConfig.value
  sortConfig.value = key === sortKey
    ? { key: sortKey, direction: direction === 'asc' ? 'desc' : 'asc' }
    : { key: sortKey, direction: 'asc' }
}

// Define function for displaying the name in the template
const capitalize = (s) => s.charAt(0).toUpperCase() + s.slice(1)

</script>

<template>
 <div class="min-w-full mx-60 mt-4">
      <div class="border">
        <table v-if="filteredData.length" class="min-w-full divide-y">
          <thead>
            <tr class="bg-green-700">
              <th v-for="key in columns"
                @click="sortBy(key)"
              class="px-6 py-3 text-start text-xs font-medium"
              >
              <div class="flex items-center justify-start gap-1">

                {{ capitalize(key) }}
              <span class="flex h-4 w-4 shrink-0 items-center justify-center" :class="{ invisible: sortConfig.key !== key }">

                <svg  class="arrow" viewBox="0 0 24 24" width="16" height="16">
                  <path v-if="sortConfig.direction === 'asc'" fill="currentColor" d="M7 14l5-5 5 5H7z" />
                  <path v-else-if="sortConfig.direction === 'desc'" fill="currentColor" d="M7 10l5 5 5-5H7z" />
                  <path v-else fill="currentColor" d="M7 10l5-5 5 5H7zM7 14l5 5 5-5H7z" />
                </svg>
              </span>  
            
              </div>
              </th> 
            </tr>
          </thead>
          <tbody class="divide-y">
            <tr v-for="(entry, i) in filteredData" :key="i" @click="selectedRow = entry.id" :class="{ selected: selectedRow && selectedRow.id === row.id }">
            
              <td v-for="key in columns" :key="key" class="px-6 py-4 whitespace-nowrap text-sm font-medium">{{ entry[key] }}</td>
            </tr>
          </tbody>
        </table>
        <p v-else>No match founds.</p>
      </div>
    </div>
</template>

<style scoped></style>