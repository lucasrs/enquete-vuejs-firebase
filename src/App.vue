<template>
  <div id="app" class="container">
    <div class="panel panel-default">
    <div class="card-panel purple darken-2"><h3 class="white-text">Enquete</h3></div>
      <h5 class="header">{{ poll.name }}</h5>
        <div class="panel-body">
          <form class="form-horizontal" method="post">
            <div v-for="(option, index) in poll.options">
              <p>
                <input v-show="!submitted" type="radio" name="id"  :value="index" :id="index" v-model="voted" class="with-gap">
                <label :for="index">{{ option.name }}</label>
                <span v-show="results">- {{ option.votes }} Votos</span>
              </p>
            </div>
            <hr>
              <a @click="vote()" class="waves-effect waves-light btn-large light-blue accent-4" v-bind:class="[ submitted == true ? 'disabled' : '' ]">Votar</a>
              <a @click="results=!results" class="waves-effect waves-light btn-large right orange darken-1">Ver Resultados</a>
          </form>
        </div>
      </div>
  </div>
</template>

<script>
import firebase from 'firebase'
import config from './../firebase-config'

firebase.initializeApp(config)
const db = firebase.database()
const ref = db.ref('poll')

export default {
  data () {
    return {
      poll: {},
      voted: '',
      submitted: false,
      results: false
    }
  },
  created () {
    const self = this
    ref.on('value', polls => self.poll = polls.val())
  },
  methods: {
    vote () {
      if (!this.submitted && this.voted >= 0) {
        let votesRef = db.ref('poll/options/' + this.voted + '/votes')
        votesRef.transaction(votes => votes +1)
        this.submitted = true
        this.results = true
      }
    }
  }
}
</script>

<style>
@import url("https://cdnjs.cloudflare.com/ajax/libs/materialize/0.97.3/css/materialize.min.css");
</style>
