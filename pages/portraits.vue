<template>
  <imagesContainer :posts="posts" />
</template>

<script>
import ImagesContainer from '../components/imagesContainer.vue'
export default {
  components: { ImagesContainer },

  async asyncData({ $content }) {
    const posts = await $content('blog')
      .where({ tags: { $contains: 'portrait' } })
      .fetch()

    return {
      posts,
    }
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
