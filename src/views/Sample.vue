<template>
  <div class="container">
    <input type="file" @change="handleFileUpload" accept=".csv" />
    <div v-if="gridData.length" class="grid-container">
      <table>
        <thead>
          <tr>
            <th v-for="(col, index) in headers" :key="index">{{ col }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in paginatedData" :key="rowIndex">
            <td v-for="(cell, cellIndex) in row" :key="cellIndex">{{ cell }}</td>
          </tr>
        </tbody>
      </table>
      <div class="pagination">
        <button @click="prevPage" :disabled="currentPage === 1">&lt; Prev</button>
        <span>Page {{ currentPage }} / {{ totalPages }}</span>
        <button @click="nextPage" :disabled="currentPage === totalPages">Next &gt;</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import Papa from 'papaparse';

const gridData = ref([]);
const headers = ref([]);
const currentPage = ref(1);
const rowsPerPage = 20;

const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (!file) return;

  Papa.parse(file, {
    complete: (result) => {
      if (result.data.length > 0) {
        headers.value = result.data[0];
        gridData.value = result.data.slice(1).filter(row => row.length > 1);
        currentPage.value = 1;
      }
    },
    skipEmptyLines: true
  });
};

const totalPages = computed(() => Math.ceil(gridData.value.length / rowsPerPage));

const paginatedData = computed(() => {
  const start = (currentPage.value - 1) * rowsPerPage;
  return gridData.value.slice(start, start + rowsPerPage);
});

const prevPage = () => {
  if (currentPage.value > 1) currentPage.value--;
};

const nextPage = () => {
  if (currentPage.value < totalPages.value) currentPage.value++;
};
</script>

<style scoped>
.container {
  text-align: center;
  margin-top: 20px;
}

.grid-container {
  margin-top: 20px;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead th {
  background: #f4f4f4;
  padding: 10px;
  border: 1px solid #ddd;
}

tbody td {
  padding: 10px;
  border: 1px solid #ddd;
  text-align: center;
}

.pagination {
  margin-top: 10px;
  display: flex;
  justify-content: center;
  gap: 10px;
}

button {
  padding: 5px 10px;
  cursor: pointer;
}
button:disabled {
  background: #ccc;
  cursor: not-allowed;
}
</style>
