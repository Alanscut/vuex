# Migrating to 4.0 from 3.x

Almost all Vuex 4 APIs have remained unchanged from Vuex 3. However, there are still a few breaking changes that you must fix.

- [Breaking Changes](#breaking-changes)
  - [Installation process](#installation-process)
  - [TypeScript support](#typescript-support)
  - [Bundles are now aligned with Vue 3](#bundles-are-now-aligned-with-vue-3)
  - ["createLogger" function is exported from the core module](#createlogger-function-is-exported-from-the-core-module)
- [New Features](#new-features)
  - [New "useStore" composition function](#new-useStore-composition-function)

## Breaking Changes

### Installation process

To align with the new Vue 3 initialization process, the installation process of Vuex has changed. To create a new store instance, users are now encouraged to use the newly introduced createStore function.

```js
import { createStore } from 'vuex'

export const store = createStore({
  state () {
    return {
      count: 1
    }
  }
})
```

To install Vuex to a Vue instance, pass the store instance instead of Vuex.

```js
import { createApp } from 'vue'
import { store } from './store'
import App from './App.vue'

const app = createApp(App)

app.use(store)

app.mount('#app')
```

:::tip NOTE
Whilst this is not technically a breaking change, you may still use the new Store(...) syntax, we recommend this approach to align with Vue 3 and Vue Router Next.
:::

### TypeScript support

Vuex 4 removes its global typings for `this.$store` within Vue Component to solve [issue #994](https://github.com/vuejs/vuex/issues/994). When used with TypeScript, you must declare your own module augmentation.

Place the following code in your project to allow `this.$store` to be typed correctly:

```ts
// vuex-shim.d.ts

import { ComponentCustomProperties } from 'vue'
import { Store } from 'vuex'

declare module '@vue/runtime-core' {
  // Declare your own store states.
  interface State {
    count: number
  }

  interface ComponentCustomProperties {
    $store: Store<State>
  }
}
```

You may learn more at [TypeScript Support](./typescript-support) page.

### Bundles are now aligned with Vue 3

The following bundles are generated to align with Vue 3 bundles:

- `vuex.global(.prod).js`
  - For direct use with `<script src="...">` in the browser. Exposes the Vuex global.
  - Global build is built as IIFE, and not UMD, and is only meant for direct use with `<script src="...">`.
  - Contains hard-coded prod/dev branches and the prod build is pre-minified. Use the `.prod.js` files for production.
- `vuex.esm-browser(.prod).js`
  - For use with native ES module imports (including module supporting browsers via `<script type="module">`.
- `vuex.esm-bundler.js`
  - For use with bundlers such as `webpack`, `rollup` and `parcel`.
  - Leaves prod/dev branches with `process.env.NODE_ENV` guards (must be replaced by bundler).
  - Does not ship minified builds (to be done together with the rest of the code after bundling).
- `vuex.cjs.js`
  - For use in Node.js server-side rendering with `require()`.

### `createLogger` function is exported from the core module

In Vuex 3, `createLogger` function was exported from `vuex/dist/logger` but it's now included in the core package. You should import the function directly from `vuex` package.

```js
import { createLogger } from 'vuex'
```

## New Features

### New `useStore` composition function

Vuex 4 introduces a new API to interact with the store in Composition API. You may use `useStore` composition function to retrieve store instance within Vue Component's `setup` hook.

```js
import { useStore } from 'vuex'

export default {
  setup () {
    const store = useStore()
  }
}
```

You may learn more at [Composition API](./composition-api) page.