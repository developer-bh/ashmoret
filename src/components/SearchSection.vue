<!-- src/components/SearchSection.vue -->
<template>
  <div class="search-section">
    <div class="bg">
      <img src="../../images/bg-search.svg" alt="Background" />
    </div>
    <div class="uk-container">
      <div class="uk-grid uk-child-width-expand@s" uk-grid>
        <div class="uk-width-1-1 uk-width-3-4@m custom-3-4">
          <div class="uk-card uk-card-default uk-card-body" uk-height-match="target: > div">
            <form>
              <div class="uk-grid uk-grid-small" uk-grid>
                <div class="uk-width-1-1 uk-width-1-2@m">
                  <input class="uk-input" type="search" placeholder="Free search" id="form-search" />
                </div>
                <div class="uk-width-1-1 uk-width-1-4@m">
                  <select class="uk-select form-area" id="form-area">
                    <option value="" disabled selected>Area</option>
                    <option v-for="area in areas" :key="area.id" :value="area.name">{{ area.name }}</option>
                  </select>
                </div>
                <div class="uk-width-1-1 uk-width-1-4@m">
                  <select class="uk-select form-category" id="form-category">
                    <option value="" disabled selected>Category</option>
                    <option v-for="category in categories" :key="category.id" :value="category.name">{{ category.name }}</option>
                  </select>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m" v-if="isAdvancedSearchVisible">
                  <select class="uk-select form-subcategory" id="form-subcategory">
                    <option value="" disabled selected>Subcategory</option>
                    <option v-for="subcategory in subcategories" :key="subcategory.id" :value="subcategory.name">{{ subcategory.name }}</option>
                  </select>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m" v-if="isAdvancedSearchVisible">
                  <select class="uk-select form-city" id="form-city">
                    <option value="" disabled selected>City</option>
                    <option v-for="city in cities" :key="city.id" :value="city.name">{{ city.name }}</option>
                  </select>
                </div>
              </div>
              <div class="uk-grid uk-grid-small" uk-grid>
                <div class="uk-width-1-1 uk-width-1-2@m uk-flex uk-flex-middle">
                  <button class="form-advanced" @click="toggleAdvancedSearch" type="button">
                    <span class="icon">
                      <img v-if="!isAdvancedSearchVisible" src="../../images/icons/icon-plus.svg" alt="Icon" />
                      <img v-else src="../../images/icons/icon-minus.svg" alt="Icon" />
                    </span>
                    <span>
                      <span v-if="!isAdvancedSearchVisible">Advanced search</span>
                      <span v-else>Hide Advanced search</span>
                    </span>
                  </button>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m">
                  <div class="uk-grid uk-flex-right uk-flex-middle">
                    <div class="uk-flex uk-flex-right uk-width-1-2">
                      <button class="form-clear" @click="resetSearch">
                        <span class="icon">
                          <img src="../../images/icons/icon-clear.svg" alt="Icon" />
                        </span>
                      Clear search
                    </button>
                    </div>
                    <div class="uk-width-1-1 uk-width-1-2@m uk-padding-remove">
                      <button class="uk-button-primary form-search">
                        Search
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </form>
          </div>
        </div>
        <div class="uk-width-1-1 uk-width-1-4@m custom-1-4">
          <div class="uk-card uk-card-default uk-card-body" uk-height-match="target: > div">
            <a href="#/" class="action-audience">
              <span class="icon">
                <img src="../../images/icons/icon-star.svg" alt="Icon" />
              </span>
              Audience choice
            </a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Papa from 'papaparse';
export default {
  data() {
    return {
      areas: [],
      categories: [],
      subcategories: [],
      cities: [],
      isAdvancedSearchVisible: false
    };
  },
  mounted() {
    this.loadData();
  },
  methods: {
    async loadData() {
      const csvFilePath = '/search_data.csv';
      Papa.parse(csvFilePath, {
        download: true,
        header: true,
        complete: (results) => {
          this.processCSVData(results.data);
        },
        error: (error) => {
          console.error('Error parsing CSV file:', error);
        }
      });
    },
    toggleAdvancedSearch() {
      this.isAdvancedSearchVisible = !this.isAdvancedSearchVisible;
    },
    processCSVData(data) {
      const categoriesSet = new Set();
      const subcategoriesSet = new Set();
      const citiesSet = new Set();
      const areasSet = new Set();

      data.forEach(row => {
        if (row.SL_BusinessType) {
          subcategoriesSet.add(row.SL_BusinessType);
        }
        if (row.SL_BusinessTypeGroup) {
          categoriesSet.add(row.SL_BusinessTypeGroup);
        }
        if (row.SL_CityArea) {
          areasSet.add(row.SL_CityArea);
        }
        if (row.SL_LocCity) {
          citiesSet.add(row.SL_LocCity);
        }
      });

      this.categories = Array.from(categoriesSet).map((name, index) => ({ id: index + 1, name }));
      this.subcategories = Array.from(subcategoriesSet).map((name, index) => ({ id: index + 1, name }));
      this.cities = Array.from(citiesSet).map((name, index) => ({ id: index + 1, name }));
      this.areas = Array.from(areasSet).map((name, index) => ({ id: index + 1, name }));
    },
    resetSearch() {
      document.getElementById('form-search').value = '';
      document.getElementById('form-area').selectedIndex = 0;
      document.getElementById('form-category').selectedIndex = 0;
      if (this.isAdvancedSearchVisible) {
        document.getElementById('form-subcategory').selectedIndex = 0;
        document.getElementById('form-city').selectedIndex = 0;
      }
    }

  }
};
</script>

<style scoped>
/* Add any styles if needed */
</style>
