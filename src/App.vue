<template>
  <div id="app">
    <h1>Gallery</h1>
    <Gallery :images="this.images" @set-image="this.openImageSlider" />
    <Slider
      :active="this.sliderActive"
      :image="this.currentImage"
      @close-slider="this.closeSlider"
      @prev-image="this.prevImage"
      @next-image="this.nextImage"
    />
  </div>
</template>

<script>
import Vue from "vue";
import Gallery from "./components/Gallery";
import Slider from "./components/Slider";

const apiKey = "23567b218376f79d9415";
const tokenUrl = "http://interview.agileengine.com/auth";
const imagesUrl = "http://interview.agileengine.com/images";

export default {
  name: "App",
  components: {
    Gallery,
    Slider,
  },
  data() {
    return {
      token: null,
      images: [],
      currentPage: 0,
      hasMore: true,
      sliderActive: false,
      currentImage: {},
    };
  },
  computed: {
    authorizationHeader() {
      return {
        headers: { Authorization: `Bearer ${this.accessToken}` },
      };
    },
  },
  created: async function () {
    await this.fetchToken();
    await this.getAllImagesRecursive();
  },
  methods: {
    fetchToken: async function () {
      const response = await fetch(tokenUrl, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ apiKey }),
      });

      const data = await response.json();
      this.accessToken = data.token;
    },
    fetchImages: async function () {
      const data = await this.safeCall(
        `${imagesUrl}?limit=3&page=${this.currentPage + 1}`
      );

      data.pictures.forEach((picture) => {
        this.images.push({
          id: picture.id,
          thumbnail: picture.cropped_picture.replace(/ /g, "%20"),
        });
      });

      this.currentPage = data.page;

      if (this.hasMore != data.hasMore) {
        this.hasMore = data.hasMore;
      }
    },
    fetchImageDetails: async function (id) {
      const data = await this.safeCall(`${imagesUrl}/${id}`);
      const image = {
        id,
        src: data.full_picture,
        thumbnail: data.cropped_picture,
        author: data.author,
        camera: data.camera,
        tags: data.tags,
      };

      Vue.set(
        this.images,
        this.images.findIndex((m) => m.id === id),
        image
      );

      this.currentImage = image;
    },
    openImageSlider: async function (image) {
      if (image.author === undefined) {
        this.fetchImageDetails(image.id);
      }

      this.currentImage = image;
      this.sliderActive = true;
    },
    closeSlider: function () {
      this.sliderActive = false;
    },
    prevImage: function () {
      const currentIndex = this.images.indexOf(this.currentImage);
      const prevIndex =
        currentIndex === 0 ? this.images.length - 1 : currentIndex - 1;

      this.openImageSlider(this.images[prevIndex]);
    },
    nextImage: function () {
      const currentIndex = this.images.indexOf(this.currentImage);
      const nextIndex =
        currentIndex === this.images.length - 1 ? 0 : currentIndex + 1;

      this.openImageSlider(this.images[nextIndex]);
    },
    getAllImagesRecursive: async function () {
      await this.fetchImages();
      if (this.hasMore) {
        this.getAllImagesRecursive();
      }
    },
    safeCall: async function (url) {
      let response = await fetch(url, this.authorizationHeader);
      if (response.status === 200) {
        return await response.json();
      } else if (response.status === 401) {
        await this.fetchToken();
        response = await fetch(url, this.authorizationHeader);
        return await response.json();
      } else {
        throw new Error("Unexpected error");
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

* {
  box-sizing: border-box;
}
</style>
