<template>
  <div>
    <!-- This is the modal -->
    <div id="location-modal" uk-modal>
      <div class="uk-modal-dialog uk-modal-body uk-margin-auto-vertical">
        <div class="modal-close">
          <button class="uk-modal-close" type="button">
            <img src="/images/icons/icon-modal-close.svg" alt="Icon" />
          </button>
        </div>
        <div class="wrapper">
          <div class="uk-text-center">
            <div class="icon">
              <img src="/images/icons/icon-modal-location.svg" alt="Icon" />
            </div>
          </div>
          <p>We need your location to provide better services. Do you agree to share your location?</p>
          <div class="buttons">
            <button class="uk-modal-close allow" type="button" @click="shareLocation">Allow</button>
            <button class="uk-modal-close decline" type="button" @click="closeModal">Decline</button>
          </div>
        </div>
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

    console.log('User coordinates:', userCoordinates);
    console.log('Location permission denied:', locationPermissionDenied);
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
