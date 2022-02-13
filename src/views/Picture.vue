<template>
  <div class="picture">
    <Rotation msg="Welcome to Your Vue.js App"
              :src="src" />
  </div>
</template>

<script>
// @ is an alias to /src
import Rotation from '@/components/RotationPicture.vue'

export default {
  name: 'Picture',
  data () {
    return {
      src: [
        { src: 'images/1.jpg' },
        { src: 'images/2.jpg' },
        { src: 'images/3.jpg' },
        { src: 'images/4.jpg' },
        { src: 'images/5.jpg' },
        { src: 'images/6.jpg' }]
    }
  },
  components: {
    Rotation
  }
}
</script>
