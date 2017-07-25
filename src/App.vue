<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{ message }}</h1>
    <input v-model="query" autofocus>
    <p>{{ answer }}</p>
    <p v-for="item in items">
      <a :href="item._source.url">{{ item._source.name }}</a>
    </p>
  </div>
</template>

<script>
  var _ = require('lodash');
  import axios from 'axios';
  
export default {
        name: 'app',
        data () {
            return {
                message: 'Your personal library. Name anything, find more.',
                query: '',
                answer: 'I cannot give you an answer until you type something!',
                items: []
            }
        },
        watch: {
            // whenever question changes, this function will run
            query: function (newSearch) {
                this.answer = 'Waiting for you to stop typing...'
                this.getAnswer()
            }
        },
        methods: {
            reverseMessage: function () {
                this.message = this.message.split('').reverse().join('')
            },

            getAnswer: _.debounce(
                function () {
                    this.answer = 'Searching...'
                    var vm = this
                    axios({
                        method: 'get',
                        url: 'http://192.168.0.99:5000/?q='+this.query,
                    })
                        .then(function (response) {
                            vm.answer = "Found "+_.capitalize(response.data.hits.total)
                            vm.items = response.data.hits.hits
                        })
                        .catch(function (error) {
                            vm.answer = 'Error! Could not reach the API. ' + error
                        })
                            },
                // This is the number of milliseconds we wait for the
                // user to stop typing.
                500
            )
        }
    }
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

input {
    width: 800px;
}

a {
  color: #42b983;
}
</style>
