# vue-plugin-timers

A Vue.JS plugin/mixin that helps set timeouts and heartbeats in your Vue components


## The Problem - Creating Heartbeats

Say we create a vue.js component

```js
export default {
  data: {
    minutes: new Date().getMinutes()
  }
}
```
What if we want minutes to update along with the user's clock
when this component is open.

```js
export default {
  get minutes() {
    return new Date().getMinutes()
  }
}
```

But, **unfortunately** this does **not work**.
Reason ?
`new Date()` or `Date.now()` type of objects are not reactive
We get their value once, but it doesn't update every minutes/second or so

## Solution - Use timers

With **`vue-plugin-timers`** we can create a `timers` option in Vue
components. Timers run a method in your component every `x` seconds
(where you can choose x).

### How to Use ?

We can activate timers in two ways

1. As a Component [Mixin](https://vuejs.org/v2/guide/mixins.html) in any component

    ```js
    // @/components/AwesomeComponent.vue <script>
    import { VueTimersMixin } from 'vue-plugin-timers'
    export default {
      mixins: [VueTimersMixin],
      ...,
      timers: { ... }
    }
    ```

2. As a global Vue [Plugin](https://vuejs.org/v2/guide/plugins.html) if you want to use in multiple components

    ```js
    // @/main.js
    import Vue from 'vue'
    import VueTimers from 'vue-plugin-timers'
    Vue.use(VueTimers)
    ```
    You can use `timers` property in all components now
    ```js
    // @/components/AnyComponent.vue <script>
    export default {
      ...,
      timers: { ... }
    }
    ```
You can define timers in 2 ways -
1. As a `timers` property in component options (in single-file-component export or inside Vue.extend())
2. If using `vue-class-component`, then there are **`@Timer`** decorators 🎉

#### Usage: `timers` component option

```js
// @/components/AwesomeComponent.vue - <script>
export default {
  data: {
    time: new Date().toTimeString()
  },
  methods: {
    updateTime() {this.time = new Date().toTimeString()}
  },
  timers: {
    /* key = name of method */
    updateMinutes: {
      /* interval of delay (default: 1000) */
      interval: 30000,
      /* repeat (default: false)
      true => setInterval, 
      false => setTimeout once */
      repeat: true
    }
  }
}
```

### Usage: `@Timer` decorator
> NOTE: This **needs** `vue-class-component` to be installed,
(and also works with `vue-property-decorator`)

```typescript
import Vue from 'vue'
import Component from 'vue-property-decorator'
import { Timer } from 'vue-plugin-timer'

@Component
class TimerComponent extends Vue {
    count = 0
    
    @Timer({ interval: 200 })
    incr() {
      this.count++
    }
}
```