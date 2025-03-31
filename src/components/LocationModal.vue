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

    // Re-run location request every 12 hours
    setInterval(() => {
      this.requestLocation();
    }, 12 * 60 * 60 * 1000);
  },
  methods: {
    requestLocation() {
      const userCoordinates = this.getItemWithExpiry('userCoordinates');

      if (userCoordinates) {
        console.log('Using cached coordinates:', userCoordinates);
        return;
      }

      if (!navigator.geolocation) {
        console.warn('Geolocation is not supported by your browser.');
        this.setItemWithExpiry('locationPermissionDenied', true);
        return;
      }

      navigator.geolocation.getCurrentPosition(
          (position) => {
            const { latitude, longitude } = position.coords;
            console.log('User coordinates:', latitude, longitude);
            this.setItemWithExpiry('userCoordinates', { latitude, longitude });
          },
          (error) => {
            if (error.code === error.PERMISSION_DENIED) {
              console.warn('Permission to access location was denied.');
              this.setItemWithExpiry('locationPermissionDenied', true);

              // Remove stored coordinates only if they existed
              if (localStorage.getItem('userCoordinates')) {
                localStorage.removeItem('userCoordinates');
              }
            } else {
              console.error('Error getting location:', error);
              this.setItemWithExpiry('locationPermissionDenied', true);
            }
          }
      );
    },
    setItemWithExpiry(key, value, ttl = 12 * 60 * 60 * 1000) {
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
