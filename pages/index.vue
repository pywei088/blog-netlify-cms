<template>
  <div
    v-if="!loading"
    class="px-4 w-full flex m-auto lg:px-10 lg:max-w-screen-lg xl:max-w-screen-xl"
  >
    <stack
      :column-min-width="
        windowWidth < 640
          ? 130
          : windowWidth < 768
          ? 200
          : windowWidth < 1024
          ? 240
          : 280
      "
      :gutter-width="windowWidth > 1024 ? 30 : 10"
      :gutter-height="windowWidth > 1024 ? 30 : 10"
      monitor-images-loaded
    >
      <stack-item
        v-for="(post, i) in posts"
        :key="i"
        style="transition: transform 700ms"
      >
        <div
          class="relative"
          @mouseover="hovered = i"
          @mouseleave="hovered = null"
        >
          <NuxtLink :to="post.slug">
            <img v-if="!loading" :src="post.src" alt="" />
          </NuxtLink>
          <transition
            enter-active-class="transition ease-in-out duration-200 transform"
            enter-class="opacity-0 scale-70"
            enter-to-class="opacity-100 scale-100"
            leave-active-class="transition ease-in-out duration-300 transform"
            leave-class="opacity-100 scale-100"
            leave-to-class="opacity-0 scale-70"
          >
            <button
              @click="showPhotoSwipe(i)"
              v-show="hovered === i"
              class="absolute shadow bottom-0 right-0 focus:outline-none m-5 bg-white rounded-full z-30"
            >
              <ExpandSvg class="p-2" />
            </button>
          </transition>
        </div>
      </stack-item>
    </stack>
    <div>
      <v-photoswipe
        v-if="!loading"
        :isOpen="isOpen"
        :items="posts"
        :options="options"
        @close="hidePhotoSwipe"
      ></v-photoswipe>
    </div>
  </div>
</template>

<script>
import ExpandSvg from '../components/ExpandSvg.vue'

export default {
  components: {
    ExpandSvg,
  },

  async asyncData({ $content }) {
    const posts = await $content('blog').fetch()

    return {
      posts,
    }
  },

  data() {
    return {
      windowWidth: null,
      loading: true,
      hovered: false,
      isOpen: false,
      options: {
        index: 0,
      },
      images: [],
    }
  },

  mounted() {
    this.loading = true
    this.windowWidth = window.innerWidth
    window.addEventListener('resize', () => {
      this.windowWidth = window.innerWidth
    })
    Promise.all(this.posts.map((post) => this.getImageData(post))).then(
      (res) => {
        res.forEach((image) => {
          this.posts.forEach((post) => {
            if (post.src === image.src) {
              this.posts[post] = Object.assign(post, image)
            }
          })
        })
        this.loading = false
      }
    )
  },

  methods: {
    head() {
      return {
        script: [
          { src: 'https://identity.netlify.com/v1/netlify-identity-widget.js' },
        ],
      }
    },
    findImg(post) {},
    showPhotoSwipe(index) {
      this.isOpen = true
      this.$set(this.options, 'index', index)
    },
    hidePhotoSwipe() {
      this.isOpen = false
    },
    async getImageData(post) {
      return await new Promise((resolve) => {
        const img = new Image()
        img.onload = () => {
          const w = img.width
          const h = img.height

          resolve({
            h,
            w,
            src: post.src,
          })
        }
        img.src = post.src
      })
    },
  },
}
</script>

<style lang="scss">
$pswp__show-hide-transition-duration: 333ms !default;
$pswp__controls-transition-duration: 333ms !default;
$pswp__background-color: #000 !default;
$pswp__placeholder-color: #222 !default;
$pswp__box-sizing-border-box: true !default; // disable .pswp * { box-sizing:border-box } (in case you already have it in your site css)
$pswp__root-z-index: 1500 !default;
$pswp__assets-path: '' !default; // path to skin assets folder (preloader, PNG and SVG sprite)
$pswp__error-text-color: #ccc !default; // "Image not loaded" text color
$pswp__include-minimal-style: true !default;

.pswp__bg {
  background-color: rgb(255, 255, 255);
  opacity: 0.85 !important;
}

.pswp__top-bar {
  background: none;
}

.pswp__button {
  width: 44px;
  height: 44px;
  position: relative;
  background: none;
  cursor: pointer;
  overflow: visible;
  -webkit-appearance: none;
  display: block;
  border: 0;
  padding: 0;
  margin: 0;
  float: right;
  opacity: 0.75;
  -webkit-transition: opacity 0.2s;
  transition: opacity 0.2s;
  -webkit-box-shadow: none;
  box-shadow: none;
}
.pswp__button:focus,
.pswp__button:hover {
  opacity: 1;
}
.pswp__button:active {
  outline: none;
  opacity: 0.9;
}
.pswp__button::-moz-focus-inner {
  padding: 0;
  border: 0;
}

/* pswp__ui--over-close class it added when mouse is over element that should close gallery */
.pswp__ui--over-close .pswp__button--close {
  opacity: 1;
}

.pswp__button,
.pswp__button--arrow--left:before,
.pswp__button--arrow--right:before {
  background: url(../static/default-skin.png) 0 0 no-repeat;
  background-size: 264px 88px;
  width: 44px;
  height: 44px;
}

@media (-webkit-min-device-pixel-ratio: 1.1),
  (-webkit-min-device-pixel-ratio: 1.09375),
  (min-resolution: 105dpi),
  (min-resolution: 1.1dppx) {
  /* Serve SVG sprite if browser supports SVG and resolution is more than 105dpi */
  .pswp--svg .pswp__button,
  .pswp--svg .pswp__button--arrow--left:before,
  .pswp--svg .pswp__button--arrow--right:before {
    background-image: url(../static/default-skin.svg);
  }
  .pswp--svg .pswp__button--arrow--left,
  .pswp--svg .pswp__button--arrow--right {
    background: none;
  }
}

.pswp__button--close {
  background-position: 0 -44px;
}

.pswp__button--share {
  background-position: -44px -44px;
}

.pswp__button--fs {
  display: none;
}

.pswp--supports-fs .pswp__button--fs {
  display: block;
}

.pswp--fs .pswp__button--fs {
  background-position: -44px 0;
}

.pswp__button--zoom {
  display: none;
  background-position: -88px 0;
}

.pswp--zoom-allowed .pswp__button--zoom {
  display: block;
}

.pswp--zoomed-in .pswp__button--zoom {
  background-position: -132px 0;
}

/* no arrows on touch screens */
.pswp--touch .pswp__button--arrow--left,
.pswp--touch .pswp__button--arrow--right {
  visibility: hidden;
}

/*
	Arrow buttons hit area
	(icon is added to :before pseudo-element)
*/
.pswp__button--arrow--left,
.pswp__button--arrow--right {
  background: none;
  top: 50%;
  margin-top: -50px;
  width: 70px;
  height: 100px;
  position: absolute;
}

.pswp__button--arrow--left {
  left: 0;
}

.pswp__button--arrow--right {
  right: 0;
}

.pswp__button--arrow--left:before,
.pswp__button--arrow--right:before {
  content: '';
  top: 35px;
  background-color: hsla(0, 0%, 100%, 0.3);
  height: 30px;
  width: 32px;
  position: absolute;
}

.pswp__button--arrow--left:before {
  left: 6px;
  background-position: -138px -44px;
}

.pswp__button--arrow--right:before {
  right: 6px;
  background-position: -94px -44px;
}

.pswp__counter,
.pswp__share-modal {
  -webkit-user-select: none;
  -moz-user-select: none;
  user-select: none;
}

.pswp__share-modal {
  display: block;
  background: rgba(0, 0, 0, 0.5);
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  padding: 10px;
  position: absolute;
  z-index: $pswp__root-z-index + 100;
  opacity: 0;
  transition: opacity 0.25s ease-out;
  -webkit-backface-visibility: hidden;
  will-change: opacity;
}

.pswp__share-modal--hidden {
  display: none;
}

.pswp__share-tooltip {
  z-index: $pswp__root-z-index + 120;
  position: absolute;
  background: #fff;
  top: 56px;
  border-radius: 2px;
  display: block;
  width: auto;
  right: 44px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.25);
  transform: translateY(6px);
  transition: transform 0.25s;
  -webkit-backface-visibility: hidden;
  will-change: transform;

  a {
    display: block;
    padding: 8px 12px;
    color: #000;
    text-decoration: none;
    font-size: 14px;
    line-height: 18px;

    &:hover {
      text-decoration: none;
      color: #000;
    }

    &:first-child {
      /* round corners on the first/last list item */
      border-radius: 2px 2px 0 0;
    }

    &:last-child {
      border-radius: 0 0 2px 2px;
    }
  }
}

.pswp__share-modal--fade-in {
  opacity: 1;

  .pswp__share-tooltip {
    transform: translateY(0);
  }
}

/* increase size of share links on touch devices */
.pswp--touch .pswp__share-tooltip a {
  padding: 16px 12px;
}

a.pswp__share--facebook {
  &:before {
    content: '';
    display: block;
    width: 0;
    height: 0;
    position: absolute;
    top: -12px;
    right: 15px;
    border: 6px solid rgba(0, 0, 0, 0);
    border-bottom-color: #fff;
    -webkit-pointer-events: none;
    -moz-pointer-events: none;
    pointer-events: none;
  }

  &:hover {
    background: #3e5c9a;
    color: #fff;

    &:before {
      border-bottom-color: #3e5c9a;
    }
  }
}

a.pswp__share--twitter {
  &:hover {
    background: #55acee;
    color: #fff;
  }
}

a.pswp__share--pinterest {
  &:hover {
    background: #ccc;
    color: #ce272d;
  }
}

a.pswp__share--download {
  &:hover {
    background: #ddd;
  }
}

/*

	3. Index indicator ("1 of X" counter)

 */

.pswp__counter {
  position: absolute;
  left: 0;
  top: 0;
  height: 44px;
  font-size: 13px;
  line-height: 44px;
  color: rgb(0, 0, 0);
  opacity: 0.75;
  padding: 0 10px;
}

/*

	4. Caption

 */

.pswp__caption {
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  min-height: 44px;

  small {
    font-size: 11px;
    color: #bbb;
  }
}

.pswp__caption__center {
  text-align: left;
  max-width: 420px;
  margin: 0 auto;
  font-size: 13px;
  padding: 10px;
  line-height: 20px;
  color: #ccc;
}

.pswp__caption--empty {
  display: none;
}

/* Fake caption element, used to calculate height of next/prev image */
.pswp__caption--fake {
  visibility: hidden;
}

/*

	5. Loading indicator (preloader)

	You can play with it here - http://codepen.io/dimsemenov/pen/yyBWoR

 */

.pswp__preloader {
  width: 44px;
  height: 44px;
  position: absolute;
  top: 0;
  left: 50%;
  margin-left: -22px;
  opacity: 0;
  transition: opacity 0.25s ease-out;
  will-change: opacity;
  direction: ltr;
}

.pswp__preloader__icn {
  width: 20px;
  height: 20px;
  margin: 12px;
}

.pswp--css_animation {
  .pswp__preloader--active {
    opacity: 1;

    .pswp__preloader__icn {
      animation: clockwise 500ms linear infinite;
    }

    .pswp__preloader__donut {
      animation: donut-rotate 1000ms cubic-bezier(0.4, 0, 0.22, 1) infinite;
    }
  }

  .pswp__preloader__icn {
    background: none;
    opacity: 0.75;
    width: 14px;
    height: 14px;
    position: absolute;
    left: 15px;
    top: 15px;
    margin: 0;
  }

  .pswp__preloader__cut {
    /*
			The idea of animating inner circle is based on Polymer ("material") loading indicator
			 by Keanu Lee https://blog.keanulee.com/2014/10/20/the-tale-of-three-spinners.html
		*/
    position: relative;
    width: 7px;
    height: 14px;
    overflow: hidden;
  }

  .pswp__preloader__donut {
    box-sizing: border-box;
    width: 14px;
    height: 14px;
    border: 2px solid #fff;
    border-radius: 50%;
    border-left-color: transparent;
    border-bottom-color: transparent;
    position: absolute;
    top: 0;
    left: 0;
    background: none;
    margin: 0;
  }
}

@media screen and (max-width: 1024px) {
  .pswp__preloader {
    position: relative;
    left: auto;
    top: auto;
    margin: 0;
    float: right;
  }
}

@keyframes clockwise {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes donut-rotate {
  0% {
    transform: rotate(0);
  }
  50% {
    transform: rotate(-140deg);
  }
  100% {
    transform: rotate(0);
  }
}

/*

	6. Additional styles

 */

/* root element of UI */
.pswp__ui {
  -webkit-font-smoothing: auto;
  visibility: visible;
  opacity: 1;
  z-index: $pswp__root-z-index + 50;
}

/* top black bar with buttons and "1 of X" indicator */
.pswp__top-bar {
  position: absolute;
  left: 0;
  top: 0;
  height: 44px;
  width: 100%;
}

.pswp__caption,
.pswp__top-bar,
.pswp--has_mouse .pswp__button--arrow--left,
.pswp--has_mouse .pswp__button--arrow--right {
  -webkit-backface-visibility: hidden;
  will-change: opacity;
  transition: opacity $pswp__controls-transition-duration
    cubic-bezier(0.4, 0, 0.22, 1);
}

/* pswp--has_mouse class is added only when two subsequent mousemove events occur */
.pswp--has_mouse {
  .pswp__button--arrow--left,
  .pswp__button--arrow--right {
    visibility: visible;
  }
}

.pswp__top-bar,
.pswp__caption {
  background: none;
}

/* pswp__ui--fit class is added when main image "fits" between top bar and bottom bar (caption) */
.pswp__ui--fit {
  .pswp__top-bar,
  .pswp__caption {
    background: none;
  }
}

/* pswp__ui--idle class is added when mouse isn't moving for several seconds (JS option timeToIdle) */

.pswp__ui--idle {
  .pswp__top-bar {
    opacity: 0;
  }

  .pswp__button--arrow--left,
  .pswp__button--arrow--right {
    opacity: 0;
  }
}

/*
	pswp__ui--hidden class is added when controls are hidden
	e.g. when user taps to toggle visibility of controls
*/
.pswp__ui--hidden {
  .pswp__top-bar,
  .pswp__caption,
  .pswp__button--arrow--left,
  .pswp__button--arrow--right {
    /* Force paint & create composition layer for controls. */
    opacity: 0.001;
  }
}

/* pswp__ui--one-slide class is added when there is just one item in gallery */
.pswp__ui--one-slide {
  .pswp__button--arrow--left,
  .pswp__button--arrow--right,
  .pswp__counter {
    display: none;
  }
}

.pswp__element--disabled {
  display: none !important;
}

@if $pswp__include-minimal-style == true {
  .pswp--minimal--dark {
    .pswp__top-bar {
      background: none;
    }
  }
}

.pswp__caption__center {
  text-transform: uppercase;
  font-weight: 500;
  text-align: center;
  max-width: 420px;
  margin: 0 auto;
  font-size: 15px;
  padding: 10px;
  line-height: 20px;
  color: rgb(27, 27, 27);
}
</style>
