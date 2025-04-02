<template>
  <img :src="currentSrc" @error="handleError"/>
</template>

<script>
export default {
  props: {
    src: String,
    fallbackSrc: {
      type: String,
      default: import.meta.env.BASE_URL + 'images/logos/logo-default.png'
    },
  },
  data() {
    return {
      currentSrc: this.imageUrl(this.src)
    };
  },
  methods: {
    handleError() {
      this.currentSrc = this.fallbackSrc;
    },
    imageUrl(logoName) {
      if (!logoName || logoName.includes('no_logo') || logoName.includes('nologo') || logoName.includes('no-logo')) {
        console.log(import.meta.env.BASE_URL + 'images/logos/logo-default.png');
        return import.meta.env.BASE_URL + 'images/logos/logo-default.png'; // Return default logo if no logo name is provided
      }

      return `https://clubs.linkc.co.il/uploads/Logos/${logoName}`;
    },
  }
};
</script>
