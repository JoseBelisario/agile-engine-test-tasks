<template>
  <div class="slider" :class="[active ? 'active' : '']">
    <div class="wrapper">
      <div class="slider-header">
        <div class="slider-close" v-on:click="onClose">X</div>
      </div>
      <div
        class="slider-body wrapper"
        :style="{ backgroundImage: 'url(' + image.src + ')' }"
      >
        <div class="arrow arrow-left" v-on:click="onPrev">←</div>
        <div class="arrow arrow-right" v-on:click="onNext">→</div>
      </div>
      <div class="slider-footer">
        <div class="wrapper">
          <div class="picture-author">Author: {{ image.author }}</div>
          <div class="picture-camera">Camera: {{ image.camera }}</div>
          <div class="picture-tags">Tags: {{ image.tags }}</div>
          <div class="share-wrapper">
            <div
              :class="[
                'picture-share',
                'icon',
                displayShareOptions ? 'active' : '',
              ]"
              v-on:click.self="toggleShareOptions"
            >
              <div
                class="share-facebook icon"
                v-on:click.self="shareOnFacebook"
              ></div>
              <div
                class="share-twitter icon"
                v-on:click.self="shareOnTwitter"
              ></div>
              <div
                class="share-whatsapp icon"
                v-on:click.self="shareOnWhatsapp"
              ></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Slider",
  props: {
    image: Object,
    active: Boolean,
  },
  data() {
    return {
      displayShareOptions: false,
    };
  },
  methods: {
    onNext() {
      this.$emit("next-image");
    },
    onPrev() {
      this.$emit("prev-image");
    },
    onClose() {
      this.$emit("close-slider");
    },
    toggleShareOptions() {
      this.displayShareOptions = !this.displayShareOptions;
    },
    shareOnFacebook() {
      window.open(
        "http://www.facebook.com/share.php?u=" + this.image.src,
        "_blank"
      );
    },
    shareOnTwitter() {
      window.open(
        "https://twitter.com/intent/tweet?text=" + this.image.src,
        "_blank"
      );
    },
    shareOnWhatsapp() {
      window.open(
        "https://web.whatsapp.com/send?text=" + this.image.src,
        "_blank"
      );
    },
  },
};
</script>

<style lang="scss">
.wrapper {
  position: relative;
  width: 100%;
  height: 100%;
}

.slider {
  position: fixed;
  top: 100%;
  left: 0;
  width: 100%;
  height: 100vh;
  transition: top 400ms ease;

  &.active {
    top: 0;
  }
}

.slider-header {
  z-index: 1;
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 60px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.6),
    rgba(0, 0, 0, 0.4)
  );
  display: flex;
  justify-content: flex-end;
  align-items: center;

  .slider-close {
    font-size: 28px;
    color: white;
    padding: 8px;
    margin-right: 12px;
    cursor: pointer;
  }
}

.slider-body {
  background-position: center;
  background-size: contain;
  background-repeat: no-repeat;
  background-color: black;

  .arrow {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    padding: 10px;
    background: #444;
    opacity: 0.8;
    cursor: pointer;
    transition: top 200ms ease;
    display: flex;
    justify-content: center;
    align-items: center;
    color: white;
    border-radius: 50%;

    &.arrow-left {
      left: 20px;
    }

    &.arrow-right {
      right: 20px;
    }

    &:hover {
      top: calc(50% - 10px);
    }
  }
}

.slider-footer {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: min-content;
  background-image: linear-gradient(
    to top,
    rgba(0, 0, 0, 0.6),
    rgba(0, 0, 0, 0.4)
  );

  & > .wrapper {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    color: white;
    font-size: 20px;
    line-height: 2;
    text-align: left;
    padding: 10px;
  }
}

.share-wrapper {
  z-index: 1;
}

.icon {
  position: absolute;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background-size: 25px;
  background-position: center;
  background-repeat: no-repeat;
  cursor: pointer;
}

.picture-share {
  bottom: 20px;
  right: 20px;
  background-color: #ea9d4c;
  z-index: auto;
  background-image: url("../assets/share-icon.png");

  .icon {
    right: 0;
    bottom: 0;
    transition: bottom 200ms ease-out;
  }
}

.share-facebook {
  z-index: -1;
  background-color: #3c579e;
  background-image: url("../assets/facebook-icon.png");
  background-size: 35px;

  .picture-share.active & {
    bottom: 180px;
  }
}

.share-twitter {
  z-index: -1;
  background-color: #55acee;
  background-image: url("../assets/twitter-icon.png");

  .picture-share.active & {
    bottom: 120px;
  }
}

.share-whatsapp {
  z-index: -1;
  background-color: #25d366;
  background-image: url("../assets/whatsapp-icon.png");
  background-size: 40px;

  .picture-share.active & {
    bottom: 60px;
  }
}
</style>