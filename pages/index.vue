<template>
  <div class="px-4 w-full flex m-auto lg:max-w-screen-lg xl:max-w-screen-xl">
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
        style="transition: transform 800ms"
      >
        <NuxtLink :to="post.slug">
          <img :src="post.img" alt="" />
        </NuxtLink>
      </stack-item>
    </stack>
  </div>
</template>

<script>
export default {
  async asyncData({ $content }) {
    const posts = await $content('blog').fetch()
    return {
      posts,
    }
  },
  data() {
    return {
      windowWidth: null,
    }
  },
  mounted() {
    this.windowWidth = window.innerWidth
    window.addEventListener('resize', () => {
      this.windowWidth = window.innerWidth
    })
  },

  methods: {
    head() {
      return {
        script: [
          { src: 'https://identity.netlify.com/v1/netlify-identity-widget.js' },
        ],
      }
    },
  },
}
</script>
