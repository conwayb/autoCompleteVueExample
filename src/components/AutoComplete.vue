<template>
  <div class='autocomplete--container'>
    <input class='search-box' type='text'
      @input="getData(search_param)" v-model="search_param"/>
    <div class='autocomplete--suggestions--container'>
      <ul v-show="suggestions.length && search_param && !selected"
          class='autocomplete--suggestions--list'>
        <li v-for="suggestion in suggestions">
          <div @click="setParam(suggestion)"
               class='suggestion'>
            <span v-for='attribute in suggestion'>
              {{ attribute }}
            </span>
          </div>
        </li>
      </ul>
      <ul v-show="!suggestions.length && search_param">
        <li>Sorry, no results</li>
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
    'urlCallback': Function, // post process url
    'dataArray': Array,
    'text_attribute': { type: String, default: 'text'}
  },
  data () {
    return {
      selected: false,
      search_param: null,
      suggestions: []
    }
  },
  methods: {
    setParam (suggestion) {
      this.search_param = suggestion[this.text_attribute];
      this.selected = true;
    },
    filterDataArray (query, things, text_attribute) {
      return things.filter(t => t[text_attribute].indexOf(query) > -1)
    },
    getData (query) {
      this.selected = false;
      if (this.url) {
        // TODO: throttle
        if (query.length >= 3) {
          let url = `${this.url}${query}`;
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
          query, this.dataArray, this.text_attribute
        );
      }
      else {
        throw new Error(
          "You must provide callback or url or dataArray prop")
      }
    }
  },
}
</script>

<style scoped></style>
