<template>
</template>

<script>
import { getCurrentInstance, ref, onMounted } from "vue";
import UIkit from "uikit";
import Icons from "uikit/dist/js/uikit-icons";

UIkit.use(Icons);

export default {
  setup() {
    const locationAvailable = ref(false);
    const userCoordinates = ref(null);

    const requestLocation = () => {
      if (!navigator.geolocation) {
        console.warn("Geolocation is not supported by your browser.");
        locationAvailable.value = false;
        return;
      }

      navigator.geolocation.getCurrentPosition(
          (position) => {
            userCoordinates.value = {
              latitude: position.coords.latitude,
              longitude: position.coords.longitude
            };
            locationAvailable.value = true;
          },
          (error) => {
            console.warn("Could not access location:", error.message);
            locationAvailable.value = false;
            userCoordinates.value = null;

            document.dispatchEvent(new CustomEvent("locationUnavailable"));
          }
      );
    };

    // Check geolocation permission on mount
    onMounted(() => {
      if (navigator.permissions) {
        navigator.permissions.query({ name: "geolocation" }).then((permissionStatus) => {
          if (permissionStatus.state === "granted") {
            requestLocation(); // Request location immediately if permission is granted
          } else {
            console.log("Geolocation permission not granted yet.");
            requestLocation();
          }
        });
      }
    });

    // Make location methods globally accessible
    const instance = getCurrentInstance();
    if (instance) {
      instance.appContext.config.globalProperties.$getUserLocation = () => userCoordinates.value;
      instance.appContext.config.globalProperties.$isLocationAvailable = () => locationAvailable.value;
    }

    return { locationAvailable, userCoordinates };
  }
};
</script>
