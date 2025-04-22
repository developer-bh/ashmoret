<template>
  <img :src="currentSrc" @error="handleError"/>
</template>

<script>

export default {
  props: {
    src: String
  },
  data() {
    return {
      fallbackSrc: new URL('/images/logos/logo-default.png', import.meta.url).href,
      currentSrc: this.imageUrl(this.src)
    };
  },
  methods: {
    handleError() {
      this.currentSrc = this.fallbackSrc;
    },
    imageUrl(logoName) {
      if (!logoName || logoName.includes('no_logo') || logoName.includes('nologo') || logoName.includes('no-logo')) {
        return new URL('/images/logos/logo-default.png', import.meta.url).href;
      }
      return `https://clubs.linkc.co.il/uploads/Logos/${logoName}`;
    }
  }
};
</script>
