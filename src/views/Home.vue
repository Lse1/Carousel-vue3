<template>
  <div class="home">
    <Rotation msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
// @ is an alias to /src
import Rotation from '@/components/Rotation.vue'

export default {
  name: 'Home',
  components: {
    Rotation
  }
}
</script>
