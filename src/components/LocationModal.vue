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
      if (!navigator.geolocation) {
        alert('Geolocation is not supported by your browser. Please enable location services in your browser settings.');
        this.setItemWithExpiry('locationPermissionDenied', true);
        return;
      }

      navigator.geolocation.getCurrentPosition(
          (position) => {
            const { latitude, longitude } = position.coords;
            console.log('User coordinates:', latitude, longitude);
            localStorage.setItem('userCoordinates', JSON.stringify({ latitude, longitude }));
          },
          (error) => {
            if (error.code === error.PERMISSION_DENIED) {
              alert('Permission to access location was denied. Please enable location services in your browser settings.');
              this.setItemWithExpiry('locationPermissionDenied', true);
              localStorage.removeItem('userCoordinates');
            } else {
              console.error('Error getting location:', error);
              this.setItemWithExpiry('locationPermissionDenied', true);
              localStorage.removeItem('userCoordinates');
            }
          }
      );
    },
    setItemWithExpiry(key, value, ttl = 12 * 60 * 60 * 1000) { // Default ttl to 12 hours in milliseconds
      const now = new Date();
      const item = {
        value: value,
        expiry: now.getTime() + ttl,
      };
      localStorage.setItem(key, JSON.stringify(item));
    },
    getItemWithExpiry(key) {
      const itemStr = localStorage.getItem(key);
      if (!itemStr) {
        return null;
      }
      const item = JSON.parse(itemStr);
      const now = new Date();
      if (now.getTime() > item.expiry) {
        localStorage.removeItem(key);
        return null;
      }
      return item.value;
    }
  }
};
</script>

<style scoped>
/* Add any styles if needed */
</style>
