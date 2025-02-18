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
            <p>{{ message }}</p>
          </div>
          <div class="buttons">
            <button class="uk-modal-close" type="button">Ok</button>
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
  name: 'NoResultModal',
  props: {
    message: {
      type: String,
      default: 'No data found'
    }
  },
  methods: {
    show() {
      // Ensure the modal element exists before showing it
      const modal = document.getElementById('no-result-modal');
      if (modal) {
        UIkit.modal(modal).show();
      } else {
        console.error('Modal element not found');
      }
    }
  }
};
</script>

<style scoped>
/* Add any styles if needed */
</style>
