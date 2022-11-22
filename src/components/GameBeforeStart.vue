<template>
  <div class="card">
    <div class="card-body">
      <div class="mb-3">
        <label for="username" class="form-label">Vad heter du?</label>
        <input
          v-model="username"
          @keyup.enter="saveUsername"
          type="username"
          class="form-control"
          id="exampleInputEmail1"
          aria-describedby="emailHelp"
        />
        <div id="usernameHelp" class="form-text">För highscore-listan ju!</div>
      </div>
      <button @click="saveUsername" class="btn btn-primary">Go!</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameBeforeStart',
  props: {
    didSaveUsername: Function
  },
  data() {
    return {
      username: null
    }
  },
  mounted() {
    const username = localStorage.getItem('matteUsername')
    if (username) {
      this.username = username
    }
  },
  methods: {
    saveUsername() {
      localStorage.setItem('matteUsername', this.username)
      this.didSaveUsername(this.username)
    }
  }
}
</script>

<style scoped>
.hello-text {
  font-size: 24px;
}
</style>
