<template>
  <div class="min-h-screen p-8 flex flex-col  flex-1 gap-6"
       :style="`background-color: ${backgroundColor}`"
  >
    <div class="flex flex-col w-full max-w-7xl mx-auto gap-6">
      <AvatarSelector @update:background="handleBackgroundUpdate"></AvatarSelector>
    </div>
  </div>
</template>

<script>
import AvatarSelector from "./components/AvatarSelector.vue";

export default {
  components: {
    AvatarSelector
  },

  data() {
    return {
      backgroundColor: '#80deea'
    }
  },
  methods: {
    handleBackgroundUpdate(backgroundColor) {
      if (backgroundColor === 'transparent') {
        this.backgroundColor = '#80deea';
        return;
      }
      this.backgroundColor = this.generateSimilarColor(backgroundColor);
    },

    generateSimilarColor(color) {
      const hexToRgb = (hex) => {
        const bigint = parseInt(hex.slice(1), 16);
        return {
          r: (bigint >> 16) & 255,
          g: (bigint >> 8) & 255,
          b: bigint & 255,
        };
      };
      const rgbToHex = ({r, g, b}) =>
          `#${((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1)}`;
      const adjustColor = (value) => Math.max(0, Math.round(value - 20));
      const rgb = hexToRgb(color);
      const darkerRgb = {
        r: adjustColor(rgb.r),
        g: adjustColor(rgb.g),
        b: adjustColor(rgb.b),
      };
      return rgbToHex(darkerRgb);
    },

  },

  mounted() {
  }
}
</script>


