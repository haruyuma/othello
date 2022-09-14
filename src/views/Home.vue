<template>
  <div class="home">
    <PlayOthello/>
  </div>
</template>

<script>
// @ is an alias to /src
import PlayOthello from '@/components/PlayOthello.vue'

export default {
  name: 'Home',
  components: {
    PlayOthello
  }
}
</script>
