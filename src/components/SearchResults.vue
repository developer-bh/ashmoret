<!-- src/components/SearchResults.vue -->
<template>
  <div class="results-section">
    <div class="uk-container">
      <div class="results">
        <ul uk-accordion>
          <li class="card" v-for="result in results" :key="result.id" >

            <div class="uk-accordion-title flex">
              <div class="results-percents">
                                    <span class="percents">
                                        <span>
                                            <span class="symbol">%</span>
                                            5
                                        </span>
                                        הנחה</span>
              </div>
              <div class="results-logo">
                <div class="results-bage" hidden>if bage</div>
                <img src="" alt="Bradex" />
              </div>
              <div class="text">
                <div class="results-title">{{ result.SL_Loc_Name }}</div>
                <div class="results-text">
                  <p>רהיטים, ספות, ספות נפתחות, מיטות, מערכות ישיבה, כורסאות</p>
                </div>
              </div>
              <div class="results-location">
                                    <span class="icon">
                                        <img src="/images/icons/icon-location.svg" alt="Icon" />
                                    </span>
                50 meters from you
              </div>
              <div class="results-icons">
                <a href="" class="results-phone">
                                            <span class="icon">
                                                <img src="/images/icons/icon-phone.svg" alt="Icon" />
                                            </span>
                </a>
                <a href="" class="results-waze">
                                            <span class="icon">
                                                <img src="/images/icons/icon-waze.svg" alt="Icon" />
                                            </span>
                </a>
              </div>
              <div class="accordion">
                                    <span class="icon">
                                        <img src="/images/icons/icon-plus-accordion.svg" alt="Icon" />
                                        <img src="/images/icons/icon-minus-accordion.svg" alt="Icon" />
                                    </span>
              </div>
            </div>

            <div class="uk-accordion-content">
              <div class="results-additional">
                <div class="flex">
                  <div class="info">
                    <div class="results-big-text">
                      <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris</p>
                    </div>
                    <div class="results-website">
                      <a href="">website</a>
                    </div>
                    <div class="results-email">
                      <a href="">email</a>
                    </div>
                    <div class="results-phone">
                      <a href="">phone</a>
                    </div>
                    <div class="results-waze">
                      <a href="">waze</a>
                    </div>
                    <div class="results-map">
                      google map
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <div class="pagination">
      <button @click="prevPage" :disabled="pageIndex === 1">Previous</button>
      <span>Page {{ pageIndex }}</span>
      <button @click="nextPage">Next</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    results: {
      type: Array,
      required: true
    },
    pageIndex: {
      type: Number,
      required: true
    }
  },
  methods: {
    prevPage() {
      this.$emit('update:pageIndex', this.pageIndex - 1);
    },
    nextPage() {
      this.$emit('update:pageIndex', this.pageIndex + 1);
    }
  }
};
</script>

<style scoped>
/* Add any styles if needed */
</style>
