<template>
  <div>
    <h4>High score</h4>
    <table class="table">
      <tr>
        <th>Spelare</th>
        <th>Poäng</th>
      </tr>
      <tr v-for="player in sortedHighscore" :key="player.username">
        <td>
          {{ player.username }}
        </td>
        <td>{{ player.points }}</td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  name: 'HighScore',
  props: {
    highscore: Object
  },
  data() {
    return {
      username: null
    }
  },
  computed: {
    sortedHighscore() {
      return Object.keys(this.highscore)
        .reduce((res, username) => {
          const playerData = {
            username,
            ...this.highscore[username]
          }
          res.push(playerData)
          return res
        }, [])
        .sort((a, b) => b.points - a.points)
    }
  }
}
</script>

<style scoped></style>
