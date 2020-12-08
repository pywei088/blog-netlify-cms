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
  props: { posts: Array },
  data() {
    return {
      windowWidth: null,
      loading: true,
      hovered: false,
      isOpen: false,
      options: {
        index: 0,
      },
    }
  },
  mounted() {
    this.loading = true
    this.windowWidth = window.innerWidth
    window.addEventListener('resize', () => {
      this.windowWidth = window.innerWidth
    })
    const randomPosts = this.posts.sort(() => Math.random() - 0.5)
    Promise.all(randomPosts.map((post) => this.getImageData(post))).then(
      (res) => {
        res.forEach((image) => {
          randomPosts.forEach((post) => {
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
