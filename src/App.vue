<template>
  <div class="container">
    <GameBeforeStart :didSaveUsername="didSaveUsername" v-if="!username" />

    <Matte v-if="username" :username="username" />
  </div>
</template>

<script>
import Matte from './components/Matte.vue'
import GameBeforeStart from './components/GameBeforeStart.vue'

export default {
  name: 'App',
  data() {
    return {
      username: null
    }
  },
  components: {
    Matte,
    GameBeforeStart
  },
  methods: {
    didSaveUsername(username) {
      this.username = username
    }
  }
}
</script>

<style></style>
