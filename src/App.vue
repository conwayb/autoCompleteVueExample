<template>
  <div id="app">
    <h2>Autocomplete Example</h2>
    <div class='event--notification'>
        You chose: {{ chosenSuggestion }}
    </div>
    <div class='wrapper'>
      <div class='example'>
        <p>With Array</p>
        <auto-complete
          @autocomplete-selected="setChoice"
          @autocomplete-clear="setChoice"
          :dataArray="horses"/>
      </div>
      <div class='example'>
        <p>With Method and custom noResultsText</p>
        <auto-complete
          :method="getHorses"
          noResultsText="Where's the beef?"/>
      </div>
      <div class='example'>
        <p>With Url</p>
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
      'horses': [
        {
          text: 'brown',
          abbr: 'br'
        },
        {
          text: 'black',
          abbr: 'bl'
        },
        {
          text: 'tall',
          abbr: 't'
        }
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
      if (value.word) this.chosenSuggestion = value.word;
      else if (value.text) this.chosenSuggestion = value.text;
    },
    getHorses (query) {
      let horses =  [
        {
          text: 'brown',
          abbr: 'br'
        },
        {
          text: 'black',
          abbr: 'bl'
        },
        {
          text: 'tall',
          abbr: 't'
        }
      ];
      return horses.filter(h => h.text.indexOf(query) > -1)
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


</style>
