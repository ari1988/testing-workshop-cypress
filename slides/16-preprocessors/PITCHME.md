## ☀️ Part 16: Preprocessors

A preprocessor is the plugin responsible for preparing a support file or a test file for the browser.

### 📚 You will learn

- how to change options when bundling specs
- how to use TypeScript specs

Also: [on.cypress.io/preprocessors-api](https://on.cypress.io/preprocessors-api)

+++

- start TodoMVC application using `npm start`
- open `16-preprocessors/spec.js`

+++
## Specs

Are bundled by default using [Cypress browserify preprocessor](https://github.com/cypress-io/cypress-browserify-preprocessor)

Or you can use [Cypress webpack preprocessor](https://github.com/cypress-io/cypress-webpack-preprocessor)

+++
## Default options

```sh
npm i -D @cypress/browserify-preprocessor
```
```js
// cypress/plugins/index.js
const browserify = require('@cypress/browserify-preprocessor')
module.exports = (on, config) => {
  on('file:preprocessor', browserify())
}
```

+++
## Changing options

```js
// cypress/plugins/index.js
const browserify = require('@cypress/browserify-preprocessor')
module.exports = (on, config) => {
  const options = browserify.defaultOptions
  options.foo = 'my value'
  on('file:preprocessor', browserify(options))
}
```

Note:
Instead of specifying all new options, modify a deep property inside the default options.

+++
## Todo: print default options

Add `debug` call to show default browserify options used to bundle spec files

```js
// cypress/plugins/index.js
const browserify = require('@cypress/browserify-preprocessor')
// convention: use name of the repo
const debug = require('debug')('testing-workshop-cypress')
module.exports = (on, config) => {
  const options = browserify.defaultOptions
  // try %o - prints object on a single line
  // or %O - prints object on multiple lines
  debug('browserify options %o', options)
  on('file:preprocessor', browserify(options))
}
```

+++

![Default options](/slides/16-preprocessors/img/default-options.png)

+++
We need to print deeper options. Use `DEBUG_DEPTH=10`

![Default options deep](/slides/16-preprocessors/img/default-options-deep.png)

+++

Includes 2 transforms by default

- [coffeeify](https://github.com/jnordberg/coffeeify) with no options
- [babelify](https://github.com/babel/babelify) with plugins and presets
  * `babel-plugin-add-module-exports`
  * `plugin-proposal-class-properties`
  * `plugin-proposal-object-rest-spread`
  * `@babel/preset-env`
  * `@babel/preset-react`

Look at the test code that transpile in `spec.js`, including JSX

+++

## Todo: add Babel plugin

Let's enable [`do` notation]() in our spec

⌨️ test "transpiles do expression"

+++

```js
const options = browserify.defaultOptions
const babelOptions = options.browserifyOptions.transform[1][1]
const babelPlugins = babelOptions.plugins
babelPlugins.push('@babel/plugin-proposal-do-expressions')
on('file:preprocessor', browserify(options))
```

+++
## Alternative: add .babelrc file

Create `.babelrc` file
```json
{
  "plugins": [
    "@babel/plugin-proposal-do-expressions"
  ]
}
```
and enable `babelOptions.babelrc = true` option.

Note:
Plugins list is concatenated from default and `.babelrc` list.

+++
## Todo: add your own plugin

Find a plugin in [https://babeljs.io/docs/en/next/plugins](https://babeljs.io/docs/en/next/plugins) and add it to the Babel plugins and write test that uses new notation. Good candidates

- function bind
- pipeline operator
- optional chaining