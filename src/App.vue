<template>
  <div id="app">
    <h3>Horses and stuff</h3>
    <p>
      With Array
      <auto-complete :dataArray="horses"/>
    </p>
    <p>
      With method
      <auto-complete :method="getHorses"/>
    </p>
    <p>
      With Url
      <auto-complete
        url="https://api.datamuse.com/words?max=10&sp="
        text_attribute="word"/>
    </p>
    <p>
      With Url and urlCallback
      <auto-complete
        url="https://api.datamuse.com/words?max=10&sp="
        text_attribute="word"
        :urlCallback="postProcess"
      />
    </p>
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
      ]
    }
  },
  methods: {
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
p {
 display: inline-block;
  margin-right: 30px;
}

</style>
