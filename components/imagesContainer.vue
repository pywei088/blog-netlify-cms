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
            enter-class="opacity-0 scale-30"
            enter-to-class="opacity-50 scale-100"
            leave-active-class="transition ease-in-out duration-200 transform"
            leave-class="opacity-50 scale-100"
            leave-to-class="opacity-0 scale-30"
          >
            <button
              @click="showPhotoSwipe(i)"
              v-show="hovered === i || isMobile()"
              class="absolute shadow opacity-75 hover:opacity-100 bottom-0 right-0 focus:outline-none m-3 md:m-5 bg-white rounded-full z-30"
            >
              <svg
                class="h-6 w-6 md:h-10 md:w-10 p-1 md:p-2 opacity-75"
                id="Layer"
                enable-background="new 0 0 64 64"
                height="512"
                viewBox="0 0 64 64"
                width="512"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  d="m26.586 34.586-14.586 14.586v-5.172c0-1.104-.896-2-2-2s-2 .896-2 2v10c0 1.104.896 2 2 2h10c1.104 0 2-.896 2-2s-.896-2-2-2h-5.172l14.586-14.586c.781-.781.781-2.047 0-2.828-.78-.781-2.048-.781-2.828 0z"
                />
                <path
                  d="m54 8h-10c-1.104 0-2 .896-2 2s.896 2 2 2h5.172l-14.586 14.586c-.781.781-.781 2.047 0 2.828.391.391.902.586 1.414.586s1.023-.195 1.414-.586l14.586-14.586v5.172c0 1.104.896 2 2 2s2-.896 2-2v-10c0-1.104-.896-2-2-2z"
                />
              </svg>
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
export default {
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
    isMobile() {
      if (
        /Android|webOS|iPhone|iPad|iPod|Opera Mini/i.test(navigator.userAgent)
      ) {
        return true
      } else {
        return false
      }
    },
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
