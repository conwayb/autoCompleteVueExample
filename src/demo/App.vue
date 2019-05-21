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
          <p>Supply a static array of objects<br>
          Listen for selected and clear events</p>
          <div class='selected--wrapper'>
              <b>You chose: {{ chosenSuggestion }}</b>
          </div>
      </div>
      <div class='example'>
        <p>With Method and custom noResultsText</p>
        <auto-complete
          :method="getColors"
          textAttribute="text"
          noResultsText="Where's the beef?"/>
          <p>Supply a method that gets called everytime user input changes<br>
          Method can be synconous or use ajax</p>
      </div>
      <div class='example'>
        <p>With API Url <em>(datamuse words API)</em></p>
        <auto-complete
          :url="endpoint"
          textAttribute="word"/>
          <p>Simple ajax case<br>
          Pass a url to which the input query is appended</p>
      </div>
      <div class='example'>
        <p>With API getUrl and urlCallback</p>
        <auto-complete
          :getUrl="constructUrl"
          :urlCallback="postProcess"
          textAttribute="word"
        />
        <p>Construct a dynamic url and postprocess results with urlCallback method</p>
      </div>
      <div class='example'>
        <p>Advanced configuration</p>
        <auto-complete
          :dataArray="prefetchedColors"
          :method="searchColorOrShade">
          <template v-slot="attributes">
            <auto-complete-item :item="attributes"/>
          </template>
        </auto-complete>
        <p>Combines a computed data array, a method to filter the array, <br>and a custom component for each autocomplete suggestion</p>
      </div>
    </div>
  </div>
</template>

<script>
import AutoCompleteItem from './components/AutoCompleteItem.vue';
import AutoComplete from './../AutoComplete.vue';

export default {
  name: 'app',
  components: {
    AutoCompleteItem,
    AutoComplete,
  },
  data () {
    return {
      colors: [
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
      prefetched_colors: [],
    }
  },
  methods: {
    setChoice (value) {
      // value is the selected suggestion object
      if (!value) {
        this.chosenSuggestion = null;
        return
      }
      if (value.text) this.chosenSuggestion = value.text;
      else this.chosenSuggestion = value;
    },
    getColors (query) {
      // this can be syncronous or ajax, eg:
      return fetch('/').then((r) => {
         return this.colors.filter(
            c => c.text.toLowerCase().indexOf(query.toLowerCase()) > -1)
      }).catch((e)=> { console.error(e) });
    },
    searchColorOrShade (query) {
      return this.prefetched_colors.filter(
         c => c.text.toLowerCase().indexOf(query.toLowerCase()) > -1 ||
         c.shade.toLowerCase().indexOf(query.toLowerCase()) > -1)
    },
    constructUrl (query) {
      return `${this.endpoint}${query}*&max=4`
    },
    postProcess (data) {
      return data.map(d => {
        delete d.score;
        return d;
      })
    }
  },
  computed: {
    prefetchedColors () {
      return this.prefetched_colors;
    }
  },
  mounted () {
    return fetch('/').then((r) => {
      this.prefetched_colors = [
        {
          text: 'Yellow',
          shade: 'lemon'
        },
        {
          text: 'Red',
          shade: 'ruby'
        },
        {
          text: 'Blue',
          shade: 'aqua'
        },
        {
          text: 'Green',
          shade: 'olive'
        },
      ]
    });
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
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
  grid-gap: 3%;
  margin: 2% 3%;
}
.example {
  display: inline-block;
  margin-right: 30px;
  height: 100vh;
}
.selected--wrapper {
  margin-top: 1em;
}
.green {
  color: green;
}
.red {
  color: red;
}
.blue {
  color: blue;
}
.yellow {
  color: yellow;
}
</style>
