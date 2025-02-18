<!-- src/components/SearchSection.vue -->
<template>
  <div class="loading-overlay" v-if="isLoading">
    <div class="spinner"></div>
  </div>
  <div class="search-section">
    <div class="bg">
      <img src="../../images/bg-search.svg" alt="Background"/>
    </div>
    <div class="uk-container">
      <div class="uk-grid uk-child-width-expand@s" uk-grid>
        <div class="uk-width-1-1 uk-width-3-4@m custom-3-4">
          <div class="uk-card uk-card-default uk-card-body" uk-height-match="target: > div">
            <form>
              <div class="uk-grid uk-grid-small" uk-grid>
                <div class="uk-width-1-1 uk-width-1-2@m">
                  <input class="uk-input" type="search" placeholder="Free search" id="form-search"/>
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
                    <option v-for="category in categories" :key="category.id" :value="category.name">{{
                        category.name
                      }}
                    </option>
                  </select>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m" v-if="isAdvancedSearchVisible">
                  <select class="uk-select form-subcategory" id="form-subcategory">
                    <option value="" disabled selected>Subcategory</option>
                    <option v-for="subcategory in subcategories" :key="subcategory.id" :value="subcategory.name">
                      {{ subcategory.name }}
                    </option>
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
                      <img v-if="!isAdvancedSearchVisible" src="../../images/icons/icon-plus.svg" alt="Icon"/>
                      <img v-else src="../../images/icons/icon-minus.svg" alt="Icon"/>
                    </span>
                    <span>
                      <span v-if="!isAdvancedSearchVisible">Advanced search</span>
                      <span v-else>Hide Advanced search</span>
                    </span>
                  </button>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m">
                  <div class="uk-grid uk-flex-right uk-flex-middle">
                    <div class="uk-flex uk-flex-right uk-width-1-2" v-if="searchResults.length > 0">
                      <button class="form-clear" @click="resetSearch">
                        <span class="icon">
                          <img src="../../images/icons/icon-clear.svg" alt="Icon"/>
                        </span>
                        Clear search
                      </button>
                    </div>
                    <div class="uk-width-1-1 uk-width-1-2@m uk-padding-remove">
                      <button class="uk-button-primary form-search" @click="performSearch">
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
                <img src="../../images/icons/icon-star.svg" alt="Icon"/>
              </span>
              Audience choice
            </a>
          </div>
        </div>
      </div>
    </div>
  </div>
  <SearchResults :results="searchResults"/>
  <NoResultModal ref="noResultModal" :message="noResultMessage"/>
</template>


<script>
import axios from 'axios';
import Papa from 'papaparse';
import SearchResults from './SearchResults.vue'; // Ensure the correct path
import NoResultModal from './NoResultModal.vue';

export default {
  components: {
    SearchResults,
    NoResultModal
  },
  data() {
    return {
      areas: [],
      categories: [],
      subcategories: [],
      cities: [],
      isAdvancedSearchVisible: false,
      searchResults: [], // Add searchResults to the data object,
      isLoading: false,
      userLocation: null,
      locationPermissionDenied: null,
      noResultMessage: '',
    };
  },
  mounted() {
    this.loadData();
    this.setFormFieldsFromUrl();
  },
  methods: {
    showNoResultModal(message) {
      this.noResultMessage = message;
      this.$refs.noResultModal.show();
    },
    async loadData() {
      const csvFilePath = import.meta.env.BASE_URL + 'search_data.csv';
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
      if (!this.isAdvancedSearchVisible) {
        document.getElementById('form-subcategory').selectedIndex = 0;
        document.getElementById('form-city').selectedIndex = 0;
      }
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

      this.categories = Array.from(categoriesSet).map((name, index) => ({id: index + 1, name}));
      this.subcategories = Array.from(subcategoriesSet).map((name, index) => ({id: index + 1, name}));
      this.cities = Array.from(citiesSet).map((name, index) => ({id: index + 1, name}));
      this.areas = Array.from(areasSet).map((name, index) => ({id: index + 1, name}));
    },
    resetSearch() {
      document.getElementById('form-search').value = '';
      document.getElementById('form-area').selectedIndex = 0;
      document.getElementById('form-category').selectedIndex = 0;
      if (this.isAdvancedSearchVisible) {
        document.getElementById('form-subcategory').selectedIndex = 0;
        document.getElementById('form-city').selectedIndex = 0;
      }
      this.searchResults = []; // Reset search results
      window.history.pushState(null, '', window.location.pathname); // Reset URL
    },
    async getFilteredResults(url) {
      return await axios.get(url);
    },
    async performSearch(event) {
      event.preventDefault();

      this.isLoading = true; // Show loading

      try {
        this.userLocation = localStorage.getItem("userCoordinates") ?? null;
        this.locationPermissionDenied = localStorage.getItem("locationPermissionDenied");

        let filter = {};

        // Collect filter inputs, but only include non-empty values
        const cityArea = document.getElementById("form-area")?.value;
        if (cityArea) filter.SL_CityArea = cityArea;

        const businessGroup = document.getElementById("form-category")?.value;
        if (businessGroup) filter.SL_BusinessTypeGroup = businessGroup;

        if (this.isAdvancedSearchVisible) {
          const locCity = document.getElementById("form-city")?.value;
          if (locCity) filter.SL_LocCity = locCity;

          const businessType = document.getElementById("form-subcategory")?.value;
          if (businessType) filter.SL_BusinessType = businessType;
        }

        const freeText = document.getElementById("form-search")?.value;
        if (freeText) filter.freeText = freeText;

        // Add user location if permission is granted
        if (this.userLocation && this.locationPermissionDenied === undefined) {
          const {latitude, longitude} = JSON.parse(this.userLocation);
          filter.SL_location = [longitude, latitude];
        }

        // Remove filter if it's empty
        const filterString = Object.keys(filter).length ? JSON.stringify(filter) : null;
        const urlParams = new URLSearchParams(window.location.search);
        if (filterString == null && !urlParams.toString()) {
          // alert("Please provide at least one search criteria.");
          this.showNoResultModal('Please provide at least one search criteria.');
          this.isLoading = false; // Hide loading
          return;
        }

        // Update URL with search parameters
        const searchParams = new URLSearchParams(filter).toString();
        window.history.pushState(null, '', `?${searchParams}`);

        // Build query parameters manually
        let queryParams = new URLSearchParams();
        queryParams.append("format", "json");
        queryParams.append("iid", "673f39ed0630441602677413");

        if (filterString) queryParams.append("filter", filterString);

        if (this.locationPermissionDenied && JSON.parse(this.locationPermissionDenied)) {
          queryParams.append("SL_BGNumberGroupOrder", "1");
        }

        // Construct the final URL
        const baseUrl = "https://cdnapi.bamboo-video.com/api/ashmoret/";
        const url = `${baseUrl}?${queryParams.toString()}`;

        const response = await this.getFilteredResults(url);
        const data = response.data.data;
        if (data.length === 0) {
          this.showNoResultModal('No results found for your search');
          this.isLoading = false;
          return;
        }
        this.searchResults = await this.processSearchResults(data);

        // this.searchResults = response.data.data;
      } catch (error) {
        console.error("Error performing search:", error);
      } finally {
        this.isLoading = false; // Hide loading
      }
    },
    async processSearchResults(data) {
      const dataWithStores = [];
      let chainStoreLocations = [];

      for (const item of data) {
        if (item.SL_CH_Code) {
          if (!chainStoreLocations[item.SL_CH_Code]) {

            const storeLocations = await this.searchChainStoreLocation(item.SL_Loc_Name);

            if (storeLocations) {
              item.storeLocations = [...storeLocations];
            }

            chainStoreLocations[item.SL_CH_Code] = item;
            dataWithStores.push(item);
          }
        } else {
          dataWithStores.push(item);
        }
      }

      return dataWithStores;
    },
    async searchChainStoreLocation(SL_Loc_Name) {
      try {
        let filter = {};

        if (this.userLocation && this.locationPermissionDenied === undefined) {
          const {latitude, longitude} = JSON.parse(this.userLocation);
          filter.SL_location = [longitude, latitude];
        }
        filter.SL_Loc_Name = SL_Loc_Name;
        const filterString = JSON.stringify(filter);

        const queryParams = new URLSearchParams();
        queryParams.append("format", "json");
        queryParams.append("filter", filterString);
        queryParams.append("iid", "673f39ed0630441602677413");

        const baseUrl = "https://cdnapi.bamboo-video.com/api/ashmoret/";
        const url = `${baseUrl}?${queryParams.toString()}`;
        const response = await axios.get(url);
        return response.data.data;
      } catch (error) {
        console.error("Error searching for chain store location:", error);
        return null;
      }
    },
    setFormFieldsFromUrl() {
      window.addEventListener('load', () => {
        const urlParams = new URLSearchParams(window.location.search);

        const cityArea = urlParams.get('SL_CityArea');
        if (cityArea) document.getElementById('form-area').value = cityArea;

        const businessGroup = urlParams.get('SL_BusinessTypeGroup');
        if (businessGroup) document.getElementById('form-category').value = businessGroup;

        const locCity = urlParams.get('SL_LocCity');
        if (locCity) {
          this.isAdvancedSearchVisible = true;
          document.getElementById('form-city').value = locCity;
        }

        const businessType = urlParams.get('SL_BusinessType');
        if (businessType) {
          this.isAdvancedSearchVisible = true;
          document.getElementById('form-subcategory').value = businessType;
        }

        const freeText = urlParams.get('freeText');
        if (freeText) document.getElementById('form-search').value = freeText;

        // Perform search if there are any parameters in the URL
        if (cityArea || businessGroup || locCity || businessType || freeText) {
          this.performSearch(new Event('submit'));
        }
      });
    },
  }
};
</script>


<style scoped>
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
}

.spinner {
  border: 10px solid #f3f3f3;
  border-top: 10px solid #3498db;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
