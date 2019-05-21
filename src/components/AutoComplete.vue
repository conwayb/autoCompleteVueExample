<template>
  <div class='autocomplete--container'>
    <input class='search-box' type='text' ref='input'
      v-on:keydown.down="moveFocus(0, $event)"
      v-on:input="getData(search_param)"
      v-model="search_param"
    />
    <div class='autocomplete--suggestions--container'
      v-show="suggestions.length && search_param && !selected">
      <ul ref="suggestions" class='autocomplete--suggestions--list'>
        <li tabindex="0" v-for="suggestion in suggestions"
            v-on:keydown.up="moveFocus(-1, $event)"
            v-on:keydown.down="moveFocus(1, $event)"
            v-on:keydown.enter="setInputValue(suggestion)">
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
    'dataArray': Array,
    'method': Function,
    'url': String, // simple case only: appends query to url;
    'getUrl': Function, // Construct url given 'query' parameters
    'urlCallback': Function, // post process data from url
    'textAttribute': { /* the attribute to display from results set */
        type: String, default: 'text'
    },
    'noResultsText': { type: String, default: 'Sorry, no results'}
  },
  data () {
    return {
      selected: false,
      search_param: null,
      suggestions: [],
    }
  },
  methods: {
    moveFocus (direction, $event) {
      if (direction == 0) {
        this.$refs.suggestions.children[0].focus()
      }
      else if (direction == 1) {
          if (document.activeElement.nextSibling) {
            document.activeElement.nextSibling.focus();
          }
      }
      else {
          if (document.activeElement == this.$refs.suggestions.children[0]) {
            this.$refs.input.focus();
          }
          if (document.activeElement.previousSibling) {
            document.activeElement.previousSibling.focus();
          }
      }
      $event.preventDefault();
    },
    setInputValue (suggestion) {
      this.search_param = suggestion[this.textAttribute]
      this.selected = true;
      this.$refs.input.focus();
      this.$emit('autocomplete-selected', suggestion);
    },
    filterDataArray (query) {
      return this.dataArray.filter(
        d => d[this.textAttribute].toLowerCase().indexOf(
          query.toLowerCase()) > -1);
    },
    getData (query) {
      this.selected = false;
      this.$emit('autocomplete-clear');
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
          let data = Promise.resolve(this.method(query)).then((data) => {
            this.suggestions = data;
          })
        }
      }
      else if (this.dataArray) {
        this.suggestions = this.filterDataArray(query);
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
  padding: 0.25em;
  font-size: inherit;
  font-family: inherit;
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
li:focus {
  background: #ccc;
}
.suggestion--attribute {
  display: inline-block;
  margin-right: 1em;
  padding: 0.1em 0;
}

</style>
