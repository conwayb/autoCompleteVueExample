<template>
  <div class='autocomplete--container'>
    <input class='search-box' type='text'
      @input="getData(search_param)" v-model="search_param"/>
    <div class='autocomplete--suggestions--container'
      v-show="suggestions.length && search_param && !selected">
      <ul class='autocomplete--suggestions--list'>
        <li v-for="suggestion in suggestions">
          <div
              @click="setInputValue(suggestion)"
              class='suggestion'>
            <div
              v-for='attribute in suggestion'
              class='suggestion--attribute'>
              {{ attribute }}
            </div>
          </div>
        </li>
      </ul>
    </div>
    <div class='autocomplete--suggestions--container'
         v-show="!suggestions.length && search_param">
      <ul class="no-results-container">
        <li class="no-results-container">
          {{ noResultsText}}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>

export default {
  name: 'auto-complete',
  props: {
    'method': Function,
    'url': String,
    'getUrl': Function,
    'urlCallback': Function, // post process url
    'dataArray': Array,
    'textAttribute': { type: String, default: 'text'},
    'noResultsText': { type: String, default: 'Sorry, no results'}
  },
  data () {
    return {
      selected: false,
      search_param: null,
      suggestions: []
    }
  },
  methods: {
    setInputValue (suggestion) {
      this.search_param = suggestion[this.textAttribute];
      this.selected = true;
      this.$emit('autocomplete-selected', suggestion);
    },
    filterDataArray (query, things, textAttribute) {
      return things.filter(t => t[textAttribute].indexOf(query) > -1)
    },
    getData (query) {
      this.selected = false;
      this.$emit('autocomplete-clear')
      let url;
      if (this.url || this.getUrl) {
        if (this.getUrl) {
          url = this.getUrl(query);
        }
        else {
          url = `${this.url}${query}`;
        }
        // TODO: throttle
        if (query.length >= 3) {
          fetch(url).then(r => {
              return r.json()
            }).then((data)=> {
              if (this.urlCallback) {
                this.suggestions = this.urlCallback(data);
                console.log(this.suggestions)
              }
              else {
                this.suggestions = data;
              }
          }).catch((e) => {
            console.log(e)
          })
        }
      }
      else if (this.method) {
        if (this.method instanceof Function) {
          this.suggestions = this.method(query);
        }
      }
      else if (this.dataArray) {
        this.suggestions = this.filterDataArray(
          query, this.dataArray, this.textAttribute
        );
      }
      else {
        throw new Error(
          "You must provide callback, url, getUrl, or dataArray prop")
      }
    }
  },
}
</script>

<style scoped>
input {
  width: 99%;
}
.autocomplete--suggestions--container {
  border: 1px solid #ccc;
  width: 100%;
  border-top: none;
}
ul  {
  margin: 0.5em;
  margin-top: -1px;
  padding: 0;
  padding-top: 0.5em;
  text-align: left;
}
li {
  list-style-type: none;
}
.suggestion {
  cursor: pointer;
}
.suggestion--attribute {
  display: inline-block;
  margin-right: 1em;
  padding: 0.1em 0;
}

</style>
