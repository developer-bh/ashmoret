<template>
  <div>
    <!-- This is the modal -->
    <div id="no-result-modal" class="default-modal" uk-modal>
      <div class="uk-modal-dialog uk-modal-body uk-margin-auto-vertical">
        <div class="modal-close">
          <button class="uk-modal-close" type="button">
            <img src="/images/icons/icon-modal-close.svg" alt="Icon" />
          </button>
        </div>
        <div class="wrapper">
          <div class="uk-text-center">
            <div class="icon">
              <svg width="48" height="48" viewBox="0 0 48 48" fill="none" xmlns="http://www.w3.org/2000/svg">
                <circle cx="24" cy="24" r="24" fill="#FFE5EF"/>
                <path d="M27.7071 26.2929C27.3166 25.9024 26.6834 25.9024 26.2929 26.2929C25.9024 26.6834 25.9024 27.3166 26.2929 27.7071L27.7071 26.2929ZM32.2929 33.7071C32.6834 34.0976 33.3166 34.0976 33.7071 33.7071C34.0976 33.3166 34.0976 32.6834 33.7071 32.2929L32.2929 33.7071ZM22 28C18.6863 28 16 25.3137 16 22H14C14 26.4183 17.5817 30 22 30V28ZM16 22C16 18.6863 18.6863 16 22 16V14C17.5817 14 14 17.5817 14 22H16ZM22 16C25.3137 16 28 18.6863 28 22H30C30 17.5817 26.4183 14 22 14V16ZM28 22C28 25.3137 25.3137 28 22 28V30C26.4183 30 30 26.4183 30 22H28ZM26.2929 27.7071L32.2929 33.7071L33.7071 32.2929L27.7071 26.2929L26.2929 27.7071Z" fill="#7D3673"/>
                <path d="M20.5858 19.1716C20.1953 18.781 19.5621 18.781 19.1716 19.1716C18.781 19.5621 18.781 20.1953 19.1716 20.5858L20.5858 19.1716ZM23.4142 24.8284C23.8047 25.2189 24.4379 25.2189 24.8284 24.8284C25.219 24.4379 25.219 23.8047 24.8284 23.4142L23.4142 24.8284ZM24.8284 20.5858C25.219 20.1953 25.219 19.5621 24.8284 19.1716C24.4379 18.781 23.8047 18.781 23.4142 19.1716L24.8284 20.5858ZM19.1716 23.4142C18.781 23.8047 18.781 24.4379 19.1716 24.8284C19.5621 25.2189 20.1953 25.2189 20.5858 24.8284L19.1716 23.4142ZM19.1716 20.5858L23.4142 24.8284L24.8284 23.4142L20.5858 19.1716L19.1716 20.5858ZM23.4142 19.1716L19.1716 23.4142L20.5858 24.8284L24.8284 20.5858L23.4142 19.1716Z" fill="#7D3673"/>
              </svg>
            </div>
            <p>{{ message }}</p>
            <div class="buttons">
              <button class="uk-modal-close allow" type="button">Close</button>
            </div>
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
