<template>
  <div>
    <input v-model="query" placeholder="Enter search term" />
    <button @click="searchData">Search</button>
    <div v-if="loading">Loading...</div>
    <div v-if="error" style="color: red;">{{ error }}</div>
    <ul>
      <li v-for="item in results" :key="item.id">{{ item.name }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: "",
      results: [],
      loading: false,
      error: null,
    };
  },
  methods: {
    async searchData() {
      if (!this.query.trim()) return;

      this.loading = true;
      this.error = null;

      try {
        const response = await fetch(
            `https://example.com/api/search?q=${encodeURIComponent(this.query)}`
        );
        const data = await response.json();
        this.results = data.results;
      } catch (err) {
        this.error = "Error fetching data";
        console.error("Fetch error:", err);
      } finally {
        this.loading = false;
      }
    },
  },
};
</script>
