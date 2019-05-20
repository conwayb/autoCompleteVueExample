<template>
  <div id="app">
    <h2>Autocomplete Example</h2>
    <p>Try Colors?</p>
    <div class='wrapper'>
      <div class='example'>
        <p>With Array and event handler</p>
        <auto-complete
          @autocomplete-selected="setChoice"
          @autocomplete-clear="setChoice"
          :dataArray="colors"/>
          <div class='selected--wrapper'>
              You chose: {{ chosenSuggestion }}
          </div>
      </div>
      <div class='example'>
        <p>With Method and custom noResultsText</p>
        <auto-complete
          :method="getColors"
          noResultsText="Where's the beef?"/>
      </div>
      <div class='example'>
        <p>With Url <em>(datamuse words API)</em></p>
        <auto-complete
          :url="endpoint"
          textAttribute="word"/>
      </div>
      <div class='example'>
        <p>With getUrl and urlCallback</p>
        <auto-complete
          :getUrl="constructUrl"
          :urlCallback="postProcess"
          textAttribute="word"
        />
      </div>
    </div>
  </div>
</template>

<script>
import AutoComplete from '@/components/AutoComplete.vue'

export default {
  name: 'app',
  components: {
    AutoComplete
  },
  data () {
    return {
      'colors': [
        {
          text: 'Yellow',
          abbr: 'Y'
        },
        {
          text: 'Red',
          abbr: 'R'
        },
        {
          text: 'Blue',
          abbr: 'B'
        },
        {
          text: 'Green',
          abbr: 'G'
        },
      ],
      endpoint: "https://api.datamuse.com/words?sp=",
      chosenSuggestion: null,
    }
  },
  methods: {
    setChoice (value) {
      if (!value) {
        this.chosenSuggestion = null;
        return
      }
      if (value.text) this.chosenSuggestion = value.text;
    },
    getColors (query) {
      return this.colors.filter(
        c => c.text.toLowerCase().indexOf(query.toLowerCase()) > -1)
    },
    constructUrl (query) {
      return `${this.endpoint}${query}&max=4`
    },
    postProcess (data) {
      return data.map(d => {
        delete d.score;
        return d;
      })
    }
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
.wrapper {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-gap: 4%;
  margin: 2% 10%;
}
.example {
  display: inline-block;
  margin-right: 30px;
  height: 100vh;
}
.selected--wrapper {
  margin-top: 1em;
}

</style>
