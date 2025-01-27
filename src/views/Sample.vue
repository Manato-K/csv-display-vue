<template>
  <div class="d-flex flex-column align-items-center vh-100 py-4">
    <div class="container" style="max-width: 1200px;">
      <h1 class="text-center mb-3">CSV File Viewer</h1>
      <div class="d-flex justify-content-between align-items-center mb-3">
        <label class="form-label mb-0">ファイルを選択</label>
        <input type="file" @change="handleFileUpload" class="form-control w-50" accept=".csv" />
      </div>
      <div class="table-container" v-if="csvData.length > 0">
        <table class="table table-bordered">
          <thead class="table-light">
            <tr>
              <th v-for="(header, index) in headers" :key="index" class="text-nowrap">{{ header }}</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(row, rowIndex) in paginatedData" :key="rowIndex">
              <td v-for="(cell, cellIndex) in row" :key="cellIndex" class="text-nowrap">{{ cell }}</td>
            </tr>
          </tbody>
        </table>
      </div>
      <nav v-if="csvData.length > 0">
        <ul class="pagination justify-content-center">
          <li class="page-item" :class="{ disabled: currentPage === 1 }">
            <button class="page-link" @click="changePage(currentPage - 1)">前へ</button>
          </li>
          <li
            class="page-item"
            :class="{ active: currentPage === page }"
            v-for="page in totalPages"
            :key="page"
          >
            <button class="page-link" @click="changePage(page)">{{ page }}</button>
          </li>
          <li class="page-item" :class="{ disabled: currentPage === totalPages }">
            <button class="page-link" @click="changePage(currentPage + 1)">次へ</button>
          </li>
        </ul>
      </nav>
      <div v-else>
        <p class="text-center mt-4">データを表示するには、CSVファイルをアップロードしてください。</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const csvData = ref([]);
const headers = ref([]);
const currentPage = ref(1);
const rowsPerPage = 10;

const totalPages = computed(() => Math.ceil(csvData.value.length / rowsPerPage));

const paginatedData = computed(() => {
  const start = (currentPage.value - 1) * rowsPerPage;
  const end = start + rowsPerPage;
  return csvData.value.slice(start, end);
});

const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (!file) return;

  const reader = new FileReader();
  reader.onload = (e) => {
    const content = e.target.result;
    parseCSV(content);
  };
  reader.readAsText(file);
};

const parseCSV = (content) => {
  const rows = content.split("\n").map((row) => row.split(","));
  headers.value = rows[0];
  csvData.value = rows.slice(1).filter((row) => row.length > 1);
  currentPage.value = 1;
};

const changePage = (page) => {
  if (page >= 1 && page <= totalPages.value) {
    currentPage.value = page;
  }
};
</script>

<style>
.container {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.table-container {
  overflow-x: auto;
  margin-bottom: 20px;
}
.table {
  table-layout: fixed;
  word-wrap: break-word;
}
.table thead th {
  white-space: nowrap;
  text-align: center;
}
.pagination .page-link {
  border-radius: 50%;
}
</style>
