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
                    <option v-for="area in areas" :key="area.id" :value="area.id">{{ area.name }}</option>
                  </select>
                </div>
                <div class="uk-width-1-1 uk-width-1-4@m">
                  <select class="uk-select form-category" id="form-category">
                    <option v-for="category in categories" :key="category.id" :value="category.id">{{ category.name }}</option>
                  </select>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m toggle-advanced">
                  <select class="uk-select form-subcategory" id="form-subcategory">
                    <option>Subcategory</option>
                    <option>1</option>
                  </select>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m toggle-advanced">
                  <select class="uk-select form-city" id="form-city">
                    <option>City</option>
                    <option>1</option>
                  </select>
                </div>
              </div>
              <div class="uk-grid uk-grid-small" uk-grid>
                <div class="uk-width-1-1 uk-width-1-2@m uk-flex uk-flex-middle">
                  <button class="form-advanced" uk-toggle="target: .toggle-advanced; cls: show" type="button">
                    <span class="icon">
                      <img class="toggle-advanced show" src="../../images/icons/icon-plus.svg" alt="Icon" />
                      <img class="toggle-advanced" src="../../images/icons/icon-minus.svg" alt="Icon" />
                    </span>
                    <span>
                      <span class="toggle-advanced show">Advanced search</span>
                      <span class="toggle-advanced">Hide Advanced search</span>
                    </span>
                  </button>
                </div>
                <div class="uk-width-1-1 uk-width-1-2@m">
                  <div class="uk-grid uk-flex-right uk-flex-middle">
                    <div class="uk-flex uk-flex-right uk-width-1-2">
                      <button class="form-clear">
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
export default {
  data() {
    return {
      areas: [],
      categories: []
    };
  },
  mounted() {
    this.loadData();
  },
  methods: {
    async loadData() {
      try {
        const response = await fetch('./../assets/data.json');
        const data = await response.json();
        this.areas = data.areas;
        this.categories = data.categories;
      } catch (error) {
        console.error('Error loading data:', error);
      }
    }
  }
};
</script>

<style scoped>
/* Add any styles if needed */
</style>
