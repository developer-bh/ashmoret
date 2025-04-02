<!-- src/components/SearchSection.vue -->
<template>
  <div class="loading-overlay" v-if="isLoading">
    <div class="spinner"></div>
  </div>
  <div class="search-section">
    <div class="bg">
      <img src="../../images/bg-search.svg" alt="Background"/>
      <img src="../../images/bg-search-mobile.svg" alt="Background"/>
    </div>
    <div class="uk-container">
      <div class="uk-grid uk-child-width-expand@s" uk-grid>
        <div class="uk-width-1-1 uk-width-3-4@m custom-3-4">
          <div class="uk-card uk-card-default uk-card-body" uk-height-match="target: > div">
            <form @submit.prevent="performSearch">
              <div class="uk-grid uk-grid-small" uk-grid>
                <div class="uk-width-1-1 uk-width-1-2@m">
                  <input class="uk-input" type="search" @keydown.enter.prevent="performSearch" placeholder="חיפוש חופשי" id="form-search"/>
                </div>
                <div class="uk-width-1-1 uk-width-1-4@m">
                  <select class="uk-select form-area" id="form-area" @keydown.enter.prevent="performSearch">
                    <option value="" disabled selected>אזור</option>
                    <option v-for="area in areas" :key="area.id" :value="area.name">{{ area.name }}</option>
                  </select>
                </div>
                <div class="uk-width-1-1 uk-width-1-4@m">
                  <select class="uk-select form-category" id="form-category" @keydown.enter.prevent="performSearch">
                    <option value="" disabled selected>סיווג</option>
                    <option v-for="category in categories" :key="category.id" :value="category.name">{{
                        category.name
                      }}
                    </option>
                  </select>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m" v-if="isAdvancedSearchVisible">
                  <select class="uk-select form-subcategory" id="form-subcategory" @keydown.enter.prevent="performSearch">
                    <option value="" disabled selected>תת-סיווג</option>
                    <option v-for="subcategory in subcategories" :key="subcategory.id" :value="subcategory.name">
                      {{ subcategory.name }}
                    </option>
                  </select>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m" v-if="isAdvancedSearchVisible">
                  <select class="uk-select form-city" id="form-city" @keydown.enter.prevent="performSearch">
                    <option value="" disabled selected>יישוב</option>
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
                      <span v-if="!isAdvancedSearchVisible">חיפוש מתקדם</span>
                      <span v-else>חיפוש מתקדם</span>
                    </span>
                  </button>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m">
                  <div class="uk-grid uk-flex-right uk-flex-middle search-wrapper">
                    <div class="uk-flex uk-flex-right uk-width-1-2" v-if="filter !== null">
                      <button class="form-clear" @click="resetSearch">
                        <span class="icon">
                          <img src="../../images/icons/icon-clear.svg" alt="Icon"/>
                        </span>
                        נקה חיפוש
                      </button>
                    </div>
                    <div class="uk-width-1-1 uk-width-1-2@m uk-padding-remove">
                      <button class="uk-button-primary form-search" type="submit">
                        חיפוש
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
            <a href="#/" class="action-audience" :class="{'grey-background': promotedShow }" @click.prevent="showPromotedItems(true)">
              <span class="icon">
                <img src="../../images/icons/icon-star.svg" alt="Icon"/>
              </span>
              המומלצים
            </a>
          </div>
        </div>
      </div>
    </div>
  </div>
  <SearchResults :results="searchResults"/>
  <div>
    <div id="no-result-modal" class="default-modal" ref="noResultModal" uk-modal>
      <div class="uk-modal-dialog uk-modal-body uk-margin-auto-vertical">
        <div class="modal-close">
          <button class="uk-modal-close" type="button" @click="closeModal">
            <img src="/images/icons/icon-modal-close.svg" alt="Icon" />
          </button>
        </div>
        <div class="wrapper">
          <div class="uk-text-center">
            <div class="icon">
              <svg width="48" height="48" viewBox="0 0 48 48" fill="none" xmlns="http://www.w3.org/2000/svg" v-if="emptyDataName === 'NoResultModal'">
                <circle cx="24" cy="24" r="24" fill="#FFE5EF"/>
                <path d="M27.7071 26.2929C27.3166 25.9024 26.6834 25.9024 26.2929 26.2929C25.9024 26.6834 25.9024 27.3166 26.2929 27.7071L27.7071 26.2929ZM32.2929 33.7071C32.6834 34.0976 33.3166 34.0976 33.7071 33.7071C34.0976 33.3166 34.0976 32.6834 33.7071 32.2929L32.2929 33.7071ZM22 28C18.6863 28 16 25.3137 16 22H14C14 26.4183 17.5817 30 22 30V28ZM16 22C16 18.6863 18.6863 16 22 16V14C17.5817 14 14 17.5817 14 22H16ZM22 16C25.3137 16 28 18.6863 28 22H30C30 17.5817 26.4183 14 22 14V16ZM28 22C28 25.3137 25.3137 28 22 28V30C26.4183 30 30 26.4183 30 22H28ZM26.2929 27.7071L32.2929 33.7071L33.7071 32.2929L27.7071 26.2929L26.2929 27.7071Z" fill="#7D3673"/>
                <path d="M20.5858 19.1716C20.1953 18.781 19.5621 18.781 19.1716 19.1716C18.781 19.5621 18.781 20.1953 19.1716 20.5858L20.5858 19.1716ZM23.4142 24.8284C23.8047 25.2189 24.4379 25.2189 24.8284 24.8284C25.219 24.4379 25.219 23.8047 24.8284 23.4142L23.4142 24.8284ZM24.8284 20.5858C25.219 20.1953 25.219 19.5621 24.8284 19.1716C24.4379 18.781 23.8047 18.781 23.4142 19.1716L24.8284 20.5858ZM19.1716 23.4142C18.781 23.8047 18.781 24.4379 19.1716 24.8284C19.5621 25.2189 20.1953 25.2189 20.5858 24.8284L19.1716 23.4142ZM19.1716 20.5858L23.4142 24.8284L24.8284 23.4142L20.5858 19.1716L19.1716 20.5858ZM23.4142 19.1716L19.1716 23.4142L20.5858 24.8284L24.8284 20.5858L23.4142 19.1716Z" fill="#7D3673"/>
              </svg>
              <svg width="48" height="48" viewBox="0 0 48 48" fill="none" xmlns="http://www.w3.org/2000/svg" v-if="emptyDataName === 'NoParametersModal'">
                <circle cx="24" cy="24" r="24" fill="#FFE5EF"/>
                <path d="M24.0498 27.45H25.0498C25.0498 26.8977 24.6021 26.45 24.0498 26.45V27.45ZM24.0498 27.55L24.0496 28.55C24.3148 28.55 24.5692 28.4447 24.7568 28.2571C24.9444 28.0696 25.0498 27.8152 25.0498 27.55H24.0498ZM23.9502 27.5499H22.9502C22.9502 28.1021 23.3978 28.5498 23.9499 28.5499L23.9502 27.5499ZM23.9502 27.45V26.45C23.3979 26.45 22.9502 26.8977 22.9502 27.45H23.9502ZM25 20.45C25 19.8977 24.5523 19.45 24 19.45C23.4477 19.45 23 19.8977 23 20.45H25ZM23 24.45C23 25.0022 23.4477 25.45 24 25.45C24.5523 25.45 25 25.0022 25 24.45H23ZM24 32C19.5817 32 16 28.4183 16 24H14C14 29.5228 18.4772 34 24 34V32ZM16 24C16 19.5817 19.5817 16 24 16V14C18.4772 14 14 18.4772 14 24H16ZM24 16C28.4183 16 32 19.5817 32 24H34C34 18.4772 29.5228 14 24 14V16ZM32 24C32 28.4183 28.4183 32 24 32V34C29.5228 34 34 29.5228 34 24H32ZM23.0498 27.45V27.55H25.0498V27.45H23.0498ZM24.0501 26.55L23.9504 26.5499L23.9499 28.5499L24.0496 28.55L24.0501 26.55ZM24.9502 27.5499V27.45H22.9502V27.5499H24.9502ZM23.9502 28.45H24.0498V26.45H23.9502V28.45ZM23 20.45V24.45H25V20.45H23Z" fill="#7D3673"/>
              </svg>
            </div>
            <p>{{ emptyDataMessage }}</p>
            <div class="buttons">
              <button class="uk-modal-close allow" type="button" @click="closeModal">סגור</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import axios from 'axios';
import Papa from 'papaparse';
import SearchResults from './SearchResults.vue';


export default {
  components: {
    SearchResults
  },
  data() {
    return {
      areas: [],
      categories: [],
      subcategories: [],
      cities: [],
      isAdvancedSearchVisible: false,
      searchResults: [], // Add searchResults to the data object,
      filter: null,
      isLoading: false,
      userLocation: null,
      locationPermissionDenied: null,
      emptyDataMessage: '',
      emptyDataName: '',
      promotedShow: false,
    };
  },
  mounted() {
    if (this.$isLocationAvailable()) {
      const {latitude, longitude} = this.$getUserLocation();
      this.userLocation = {latitude, longitude};
    }
    this.locationPermissionDenied = !this.$isLocationAvailable();

    this.loadData();
    this.setFormFieldsFromUrl();
    this.loadPromotedItemsOnPageLoad();
  },
  methods: {
    showEmptyDataModal(name, message) {
      this.emptyDataName = name;
      this.emptyDataMessage = message;
      let localUikit = UIkit
      // console.log();
      if (this.$refs.noResultModal) {
        const modal = localUikit.modal(this.$refs.noResultModal);
        modal.show();
        this.searchResults = [];
      } else {
        console.error("Modal reference is not available.");
      }
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
    resetSearch(event) {
      event.preventDefault();
      document.getElementById('form-search').value = '';
      document.getElementById('form-area').selectedIndex = 0;
      document.getElementById('form-category').selectedIndex = 0;
      if (this.isAdvancedSearchVisible) {
        document.getElementById('form-subcategory').selectedIndex = 0;
        document.getElementById('form-city').selectedIndex = 0;
      }
      this.searchResults = []; // Reset search results
      window.history.pushState({}, document.title, window.location.pathname); // Reset URL
      this.filter = null;
      this.loadPromotedItemsOnPageLoad();
    },
    async getFilteredResults(url) {
      return await axios.get(url);
    },
    async performSearch(event) {
      event.preventDefault();
      this.isLoading = true; // Show loading
      this.promotedShow = false;

      try {
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

        if (Object.keys(filter).length === 0) {
          this.showEmptyDataModal('NoParametersModal', 'חסרים פרמטרים לחיפוש');
          this.isLoading = false; // Hide loading
          return;
        }

        // Remove filter if it's empty
        const filterString = Object.keys(filter).length ? JSON.stringify(filter) : null;
        this.filter = filterString;

        // Update URL with search parameters, excluding user location
        const searchParams = new URLSearchParams(filter).toString();
        window.history.pushState(null, '', `?${searchParams}`);

        // Build query parameters manually
        let queryParams = new URLSearchParams();
        queryParams.append("format", "json");
        queryParams.append("iid", "673f39ed0630441602677413");

        if (filterString) queryParams.append("filter", filterString);

        if (!this.userLocation) {
          queryParams.append("sort", "{\"SL_BGNumberGroupOrder\":1}");
        }

        // Construct the final URL
        const baseUrl = "https://cdnapi.bamboo-video.com/api/ashmoret/";
        const url = `${baseUrl}?${queryParams.toString()}`;

        const response = await this.getFilteredResults(url);
        const data = response.data.data;
        if (!data || data.length === 0) {
          this.showEmptyDataModal('NoResultModal', 'מצטערים, לא מצאנו את הפריט שחיפשת');
          this.isLoading = false;
          return;
        }
        this.searchResults = await this.processSearchResults(data);
      } catch (error) {
        console.error("Error performing search:", error);
      } finally {
        this.isLoading = false; // Hide loading
      }
    },
    async processSearchResults(data) {
      const dataWithStores = [];
      const chainStoreLocations =  new Map();
      const now = new Date();

      for (const item of data) {
        const chCode = item.SL_CH_Code ?? -1;
        const createTime = new Date(item.SL_CreateTime);
        const hoursDifference = Math.floor((now - createTime) / (1000 * 60 * 60));

        if (chCode > 0) {
          if (!chainStoreLocations.has(chCode)) {
            item.storeLocations = [item];
            item.amountOfNewChainStores = hoursDifference < 24 ? 1 : 0;
            item.amountOfChainStore = hoursDifference >= 24 ? 1 : 0;
            chainStoreLocations.set(chCode, item);
            dataWithStores.push(item);
          } else {
            const existingItem = chainStoreLocations.get(chCode);
            existingItem.storeLocations.push(item);
            if (hoursDifference < 24) {
              existingItem.amountOfNewChainStores = (existingItem.amountOfNewChainStores || 0) + 1;
            } else {
              existingItem.amountOfChainStore = (existingItem.amountOfChainStore || 0) + 1;
            }
          }
        } else {
          dataWithStores.push(item);
        }
      }

      const promotedItems = dataWithStores.filter(item => item.isPromoted === 1);
      const nonPromotedItems = dataWithStores.filter(item => item.isPromoted !== 1);

      // Combine promoted items at the beginning of the list
      const sortedDataWithStores = [...promotedItems, ...nonPromotedItems];


      return sortedDataWithStores;
    },
    setFormFieldsFromUrl() {
      window.addEventListener('load', () => {
        const urlParams = new URLSearchParams(window.location.search);

        const cityArea = urlParams.get('SL_CityArea');
        if (cityArea) document.getElementById('form-area').value = cityArea;

        const businessGroup = urlParams.get('SL_BusinessTypeGroup');
        if (businessGroup) document.getElementById('form-category').value = businessGroup;

        const locCity = urlParams.get('SL_LocCity');
        if (locCity && document.getElementById('form-city') !== null) {

          document.getElementById('form-city').value = locCity;
          this.isAdvancedSearchVisible = true;
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
    async loadPromotedItemsOnPageLoad() {
      this.promotedShow = false;
      try {
        let queryParams = new URLSearchParams();
        queryParams.append("format", "json");
        let filter = {};
        filter.isPromoted = 1;

        queryParams.append("filter", JSON.stringify(filter));
        queryParams.append("iid", "673f39ed0630441602677413");

        if (this.userLocation) {
          const { latitude, longitude } = this.userLocation;
          queryParams.append("location", JSON.stringify([longitude, latitude]));
        }

        const baseUrl = "https://cdnapi.bamboo-video.com/api/ashmoret/";
        const url = `${baseUrl}?${queryParams.toString()}`;

        const response = await axios.get(url);
        const data = response.data.data;

        if (data && data.length > 0) {
          const dataWithStores = [];
          const chainStoreLocations = new Map();
          const now = new Date();
          for (const item of data) {
            const chCode = item.SL_CH_Code ?? -1;
            const createTime = new Date(item.SL_CreateTime);
            const hoursDifference = Math.floor((now - createTime) / (1000 * 60 * 60));

            if (chCode > 0) {
              if (!chainStoreLocations.has(chCode)) {
                item.storeLocations = [item];
                item.amountOfNewChainStores = hoursDifference < 24 ? 1 : 0;
                item.amountOfChainStore = hoursDifference >= 24 ? 1 : 0;
                chainStoreLocations.set(chCode, item);
                dataWithStores.push(item);
              } else {
                const existingItem = chainStoreLocations.get(chCode);
                existingItem.storeLocations.push(item);
                if (hoursDifference < 24) {
                  existingItem.amountOfNewChainStores = (existingItem.amountOfNewChainStores || 0) + 1;
                } else {
                  existingItem.amountOfChainStore = (existingItem.amountOfChainStore || 0) + 1;
                }
              }
            } else {
              dataWithStores.push(item);
            }
            const promotedItems = dataWithStores.slice(0, 5);
            this.searchResults = promotedItems;
          }
        }
      } catch (error) {
        console.error("Error loading promoted items on page load:", error);
      }
    },
    showPromotedItems(showedOnClick = false) {
      this.resetSearch(new Event('submit'));
      this.loadPromotedItemsOnPageLoad();
      this.promotedShow = showedOnClick;
    },
  },
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

.grey-background {
  background-color: grey;
}
</style>
