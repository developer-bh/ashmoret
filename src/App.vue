<template>
  <div class="main">
    <LocationModal />
    <SearchSection />
  </div>
</template>

<script>
import SearchSection from './components/SearchSection.vue';
import LocationModal from './components/LocationModal.vue';

export default {
  components: {
    SearchSection,
    LocationModal
  },
};
</script>

<style scoped>
/* Add any styles if needed */
</style>
