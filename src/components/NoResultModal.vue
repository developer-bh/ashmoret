<template>
  <div>
    <!-- This is the modal -->
    <div id="no-result-modal" uk-modal>
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
            <button class="uk-modal-close allow" type="button" @click="shareno-result">Allow</button>
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

</script>

<style scoped>
/* Add any styles if needed */
</style>
