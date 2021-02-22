<template>
  <div class="gallery-grid">
    <div
      class="gallery-grid-image"
      v-for="image in images"
      :key="image.id"
      :style="{ backgroundImage: 'url(' + image.thumbnail + ')' }"
      v-on:click="setImage(image)"
    ></div>
  </div>
</template>

<script>
export default {
  name: "Gallery",
  props: {
    images: Array,
  },
  methods: {
    setImage(image) {
      this.$emit('set-image', image);
    }
  }
};
</script>

<style lang="scss">
.gallery-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-auto-rows: minmax(calc(50vw - 15px), 1fr);
  gap: 10px;
  justify-content: center;

  @media (min-width: 768px) {
    grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
    grid-auto-rows: minmax(240px, 1fr);
    gap: 15px;
  }
}

.gallery-grid-image {
  position: relative;
  background-position: center;
  background-size: cover;

  &:after {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: #222;
    opacity: 0;
    transition: opacity 200ms ease-in;
    cursor: pointer;
  }

  &:hover:after {
    opacity: 0.8;
  }
}
</style>