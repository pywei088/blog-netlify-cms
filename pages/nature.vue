<template>
  <imagesContainer :posts="posts" />
</template>

<script>
import ImagesContainer from '../components/imagesContainer.vue'

export default {
  components: {
    ImagesContainer,
  },

  async asyncData({ $content }) {
    const posts = await $content('blog')
      .where({ tags: { $contains: 'nature' } })
      .fetch()

    return {
      posts,
    }
  },
}
</script>
