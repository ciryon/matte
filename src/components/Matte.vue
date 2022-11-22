<template>
  <div v-if="!started && !roundEnded" class="card mt-3">
    <div class="card-body d-flex justify-content-center">
      <div class="d-flex align-items-center">
        <span>Multiplikationstabell:</span>
        <input
          v-model="currentTable"
          @change="getNewProblem"
          min="0"
          max="12"
          type="number"
          class="ms-3 form-control table-input"
        />
      </div>
    </div>
    <div class="card-body d-flex justify-content-center">
      <button @click="didStart" class="btn btn-lg btn-rounded btn-primary">
        STARTA!
      </button>
    </div>
  </div>
  <div v-if="roundEnded" class="card mt-3">
    <div class="card-body">
      <h3>Tiden är ute!</h3>
      <HighScore v-if="highscore" :highscore="highscore" />
      <button @click="didStart" class="btn btn-lg btn-rounded btn-primary">
        Prova igen
      </button>
    </div>
  </div>
  <div v-if="started && !roundEnded">
    <div class="card">
      <div class="card-header d-flex justify-content-between">
        <div class="d-flex">
          <span class="text-info me-3">{{ username }}</span>
          <span
            >Poäng: <b>{{ points }}</b></span
          >
          <span class="ms-3"
            >Streak: <b>{{ streak }}</b></span
          >
        </div>
        <h5 :class="timerCount <= 10 ? 'text-danger' : 'text-muted'">
          {{ timerCount }}
        </h5>
      </div>
      <div
        class="task mt-3 d-flex justify-content-center"
        v-if="currentProblem"
      >
        <div>
          <h3 class="mt-3">
            {{ currentProblem.title }}
          </h3>
        </div>
      </div>
      <div>
        <div
          v-if="!didRespond"
          class="card-footer d-flex align-items-center justify-content-center"
        >
          <input
            type="number"
            v-model="answer"
            class="form-control me-4 answer-input"
            min="0"
            max="144"
            @keyup.enter="answerToProblem"
          />
          <button
            @click="answerToProblem"
            v-if="answer !== null"
            class="btn btn-rounded btn-primary"
          >
            Svara
          </button>
        </div>
        <div class="mt-5 mb-5 ms-5 me-5" v-if="didRespond">
          <div
            class="alert row"
            :class="answerIsCorrect ? 'alert-success' : 'alert-danger'"
          >
            <div class="answer-info col col-md-6">
              <h4>
                Ditt svar {{ answer }} är
                {{ answerIsCorrect ? 'RÄTT' : 'FEL' }}!
              </h4>
              <p v-if="!answerIsCorrect">Rätt svar var {{ solution }}</p>
              <button
                @click="getNewProblem"
                :class="`mt-3 btn btn-rounded btn-${
                  answerIsCorrect ? 'success' : 'danger'
                }`"
              >
                Ny uppgift
              </button>
            </div>
            <div class="col" v-if="highscore">
              <HighScore :highscore="highscore" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import HighScore from './HighScore.vue'
export default {
  name: 'MatteComponent',
  props: {
    username: String
  },
  components: {
    HighScore
  },
  data() {
    return {
      roundDuration: 60,
      started: false,
      roundEnded: false,
      timerCount: 0,
      timeEllapsed: 0,
      points: 0,
      streak: 0,
      currentTable: 8,
      currentProblem: null,
      answer: null,
      didRespond: false,
      highscore: null
    }
  },

  mounted() {
    // this.didStart()
  },
  computed: {
    answerIsCorrect() {
      if (this.currentProblem && this.answer !== null) {
        return this.answer === this.solution
      } else {
        return false
      }
    },
    solution() {
      if (this.currentProblem && this.answer !== null) {
        return this.currentProblem.first * this.currentProblem.second
      } else {
        return 0
      }
    }
  },

  watch: {
    timerCount: {
      handler(value) {
        if (value > 0) {
          setTimeout(() => {
            this.timerCount--
          }, 1000)
        } else {
          this.roundEnded = true
          this.createHighscore()
        }
      },
      immediate: false // triggered upon creation
    }
  },

  methods: {
    createHighscore() {
      const highscore = JSON.parse(
        localStorage.getItem('matteHighscore') || '{}'
      )
      if (highscore && Object.keys(highscore).length) {
        this.highscore = highscore
      }
    },
    didStart() {
      this.getNewProblem()
      this.started = true
      this.roundEnded = false
      this.timerCount = this.roundDuration
    },
    getNewProblem() {
      this.didRespond = false
      this.answer = null
      this.highscore = null
      const first = Math.round(Math.random(10) * 10)
      const second = this.currentTable
      this.currentProblem = {
        first,
        second,
        title: `Vad är ${first} · ${second} ?`
      }
    },
    answerToProblem() {
      this.didRespond = true
      if (this.answerIsCorrect) {
        this.points++
        this.streak++
        this.saveHighscore()
      } else {
        this.streak = 0
        this.createHighscore()
      }
    },
    saveHighscore() {
      const highscore = JSON.parse(
        localStorage.getItem('matteHighscore') || '{}'
      )
      if (
        !highscore[this.username] ||
        highscore[this.username].points < this.points
      ) {
        highscore[this.username] = { points: this.points }
      }
      localStorage.setItem('matteHighscore', JSON.stringify(highscore))
    }
  }
}
</script>

<style scoped>
.answer-input,
.table-input {
  width: 100px;
}
</style>
