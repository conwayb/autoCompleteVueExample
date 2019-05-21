# autocomplete

A configurable autocomplete widget written in Vue.js.
See the demo for examples (run `yarn serve`).
Properties can be combined for more advanced usage.

Data can be provided as an array of objects or can be fetched from an endpoint on each user input change (TODO: throttling).

This is a work-in-progress and could use a lot of refinement to be made more useful in general application. PRs welcome.

### Props

- `dataArray`: A static array of objects to be used for autocomplete. This could be a computed property if you eg: wanted to load this array one time from an endpoint on page load (see examples).

- `method`: Method to be called each time user input changes. Receives user input query as a parameter.

- `url`: A simple static endpoint. User input will be appended to this url.

- `getUrl`: A method that returns a constructed url. Receives user input query as a parameter.

- `urlCallback`: A function that processes data after its returned from url or getUrl. Receives JSON response as returned from url or getUrl as parameter.

- `textAttribute`: The object attribute (from dataArray objects or objects returned from an endpoint) to filter against. Defaults to 'text'.

- `noResultsText`: Custom no results message. Default: 'Sorry, no results'


## Project setup
```
yarn install
```

### Compile and hot-reloads for development
```
yarn run serve
```

### Add to other project

This is not in npm currently, but you can play around with it by installing from this repo by adding the following to your package.json dependencies:
```
"@wdt/autocomplete": "conwayb/AutoCompleteVueExample"
```
