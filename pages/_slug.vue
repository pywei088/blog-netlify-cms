<template>
  <article class="w-full px-4">
    <header class="mt-16 text-center max-w-screen-md m-auto">
      <h1 class="text-4xl font-bold tracking-wide">{{ post.title }}</h1>
      <p class="mt-5 text-gray-600 text-xl">{{ post.description }}</p>
    </header>
    <img
      class="pt-16 ml-0 mr-auto w-781px lg:w-1140px lg:m-auto"
      :src="post.src"
      alt=""
    />
    <nuxt-content
      class="max-w-screen-md m-auto mt-16 leading-7"
      :document="post"
    />
  </article>
</template>

<script>
export default {
  async asyncData({ $content, params, error }) {
    let post
    try {
      post = await $content('blog', params.slug).fetch()
    } catch (e) {
      error({ message: 'Blog Post not found' })
    }
    return {
      post,
    }
  },
}
</script>

<style>
.nuxt-content img {
  width: 300px;
}
.nuxt-content p {
  padding: 0.75rem 0;
}
</style>
