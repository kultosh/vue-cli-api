<template>
  <div>
    <Navbar></Navbar>
    <div class="container">
      <Article></Article>
    </div>
  </div>
</template>

<script>
import Article from './components/Article.vue'
import Navbar from './components/Navbar.vue'

export default {
  components: {
      Article,
      Navbar,
  },
}
</script>