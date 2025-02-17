<template>
  <div>
    <!-- The modal is removed as we are using the browser's location request -->
  </div>
</template>

<script>
import UIkit from 'uikit';
import Icons from 'uikit/dist/js/uikit-icons';

UIkit.use(Icons);

export default {
  mounted() {
    this.requestLocation();
  },
  methods: {
    requestLocation() {
      navigator.geolocation.getCurrentPosition(
          (position) => {
            const { latitude, longitude } = position.coords;
            console.log('User coordinates:', latitude, longitude);
            localStorage.setItem('userCoordinates', JSON.stringify({ latitude, longitude }));
          },
          (error) => {
            if (error.code === error.PERMISSION_DENIED) {
              alert('Permission to access location was denied. Please enable location services in your browser settings.');
              localStorage.setItem('locationPermissionDenied', true);
            } else {
              console.error('Error getting location:', error);
              localStorage.setItem('locationPermissionDenied', true);
            }
          }
      );
    }
  }
};
</script>

<style scoped>
/* Add any styles if needed */
</style>
