<!-- FuzzySearch.vue -->
<template>
    <div>
      <input type="text" v-model="searchQuery" placeholder="Search countries..." @input="performSearch" />
      <div>
        <label for="sortCriteria">Sort by: </label>
        <button @click="sortOrder = 'asc'; performSearch()">Ascending</button>
        <button @click="sortOrder = 'desc'; performSearch()">Descending</button>
      </div>
      <div>
        <button @click="changePage(currentPage - 1)" :disabled="currentPage === 1">Previous</button>
        <span>Page {{ currentPage }} of {{ totalPages }}</span>
        <button @click="changePage(currentPage + 1)" :disabled="currentPage === totalPages">Next</button>
      </div>
    </div>
  </template>
  
  <script>
  import Fuse from 'fuse.js';
  
  export default {
    props: {
      countries: {
        type: Array,
        required: true
      }
    },
    data() {
      return {
        searchQuery: '',
        searchResults: [],
        currentPage: 1,
        rowsPerPage: 25,
        fuse: null,
        sortCriteria: 'name.common',
        sortOrder: 'asc'
      };
    },
    watch: {
      countries: {
        handler() {
          this.setupFuse();
          this.performSearch();
        },
        immediate: true
      },
      searchResults(newResults) {
        this.$emit('update-results', this.paginateResults(newResults));
      },
      currentPage() {
        this.$emit('update-results', this.paginateResults(this.searchResults));
      }
    },
    methods: {
      setupFuse() {
        const options = {
          keys: [
            'name.common',
            'name.official',
            'name.nativeName.ind.official',
            'name.nativeName.ind.common',
            'name.nativeName.bar.official',
            'name.nativeName.bar.common',
            'name.nativeName.ger.official',
            'name.nativeName.ger.common'
          ],
          threshold: 0.3
        };
        this.fuse = new Fuse(this.countries, options);
        this.searchResults = this.countries.map(country => ({ item: country }));
      },
      performSearch() {
        if (this.searchQuery) {
          this.searchResults = this.fuse.search(this.searchQuery);
        } else {
          this.searchResults = this.countries.map(country => ({ item: country }));
        }
        this.sortResults();
        this.currentPage = 1;
        this.$emit('update-results', this.paginateResults(this.searchResults));
      },
      sortResults() {
        this.searchResults.sort((a, b) => {
          const fieldA = this.getField(a.item, this.sortCriteria);
          const fieldB = this.getField(b.item, this.sortCriteria);
          if (fieldA < fieldB) return this.sortOrder === 'asc' ? -1 : 1;
          if (fieldA > fieldB) return this.sortOrder === 'asc' ? 1 : -1;
          return 0;
        });
      },
      getField(item, field) {
        return field.split('.').reduce((o, i) => o[i], item);
      },
      paginateResults(results) {
        const start = (this.currentPage - 1) * this.rowsPerPage;
        const end = start + this.rowsPerPage;
        return results.slice(start, end);
      },
      changePage(page) {
        if (page > 0 && page <= this.totalPages) {
          this.currentPage = page;
        }
      }
    },
    computed: {
      totalPages() {
        return Math.ceil(this.searchResults.length / this.rowsPerPage);
      }
    }
  };
  </script>
  