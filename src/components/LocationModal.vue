<template>
  <div>
    <!-- This is the modal -->
    <div id="location-modal" uk-modal>
      <div class="uk-modal-dialog uk-modal-body">
        <h2 class="uk-modal-title">Share your location</h2>
        <p>We need your location to provide better services. Do you agree to share your location?</p>
        <button class="uk-button uk-button-primary" @click="shareLocation">Yes</button>
        <button class="uk-button uk-modal-close" @click="closeModal">No</button>
      </div>
    </div>
  </div>
</template>

<script>
import UIkit from 'uikit';
import Icons from 'uikit/dist/js/uikit-icons';

UIkit.use(Icons);
export default {
  mounted() {
    const userCoordinates = localStorage.getItem('userCoordinates');
    const locationPermissionDenied = localStorage.getItem('locationPermissionDenied');

    if (!userCoordinates && !locationPermissionDenied) {
      UIkit.modal('#location-modal').show();
    }
  },
  methods: {
    shareLocation() {
      navigator.geolocation.getCurrentPosition(
          (position) => {
            const { latitude, longitude } = position.coords;
            console.log('User coordinates:', latitude, longitude);
            localStorage.setItem('userCoordinates', JSON.stringify({ latitude, longitude }));
            UIkit.modal('#location-modal').hide();
          },
          (error) => {
            if (error.code === error.PERMISSION_DENIED) {
              alert('Permission to access location was denied. Please enable location services in your browser settings.');
              localStorage.setItem('locationPermissionDenied', true);
            } else {
              console.error('Error getting location:', error);
              localStorage.setItem('locationPermissionDenied', true);
            }
            UIkit.modal('#location-modal').hide();
          }
      );
    },
    closeModal() {
      UIkit.modal('#location-modal').hide();
      localStorage.setItem('locationPermissionDenied', true);
    }
  }
};
</script>

<style scoped>
/* Add any styles if needed */
</style>
