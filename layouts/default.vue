<template>
  <div>
    <TopNavBar />
    <Nuxt />
  </div>
</template>

<script>
import TopNavBar from '../components/TopNavBar.vue'
export default {
  components: { TopNavBar },
}
</script>

<style>
* {
  font-family: 'Rubik', 'open-sans';
}
</style>
