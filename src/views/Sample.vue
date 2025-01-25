<template>
  <div class="container mt-4">
    <h1 class="mb-4">CSV Viewer</h1>
    <div class="mb-3">
      <input type="file" @change="handleFileUpload" class="form-control" accept=".csv" />
    </div>
    <div v-if="csvData.length > 0">
      <table class="table table-striped">
        <thead>
          <tr>
            <th v-for="(header, index) in headers" :key="index">{{ header }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in paginatedData" :key="rowIndex">
            <td v-for="(cell, cellIndex) in row" :key="cellIndex">{{ cell }}</td>
          </tr>
        </tbody>
      </table>
      <nav>
        <ul class="pagination justify-content-center">
          <li class="page-item" :class="{ disabled: currentPage === 1 }">
            <button class="page-link" @click="changePage(currentPage - 1)">Previous</button>
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
            <button class="page-link" @click="changePage(currentPage + 1)">Next</button>
          </li>
        </ul>
      </nav>
    </div>
    <div v-else>
      <p>No data to display. Please upload a CSV file.</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      csvData: [], // CSVデータを格納
      headers: [], // CSVの列ヘッダー
      currentPage: 1, // 現在のページ番号
      rowsPerPage: 20, // 1ページあたりの行数
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.csvData.length / this.rowsPerPage);
    },
    paginatedData() {
      const start = (this.currentPage - 1) * this.rowsPerPage;
      const end = start + this.rowsPerPage;
      return this.csvData.slice(start, end);
    },
  },
  methods: {
    handleFileUpload(event) {
      const file = event.target.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = (e) => {
        const content = e.target.result;
        this.parseCSV(content);
      };
      reader.readAsText(file);
    },
    parseCSV(content) {
      const rows = content.split("\n").map((row) => row.split(","));
      this.headers = rows[0];
      this.csvData = rows.slice(1).filter((row) => row.length > 1); // 空行を除外
      this.currentPage = 1; // ページをリセット
    },
    changePage(page) {
      if (page >= 1 && page <= this.totalPages) {
        this.currentPage = page;
      }
    },
  },
};
</script>

<style>
/* スタイリングを調整したい場合に使用 */
</style>
