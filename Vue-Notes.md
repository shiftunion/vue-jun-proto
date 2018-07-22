
# Vue Notes - Pluralsight - vuejs-fundamentals
https://app.pluralsight.com/library/courses/vuejs-fundamentals/table-of-contents
https://github.com/jmcooper/vuejs-fundamentals/tree/master/src

## CSS
`Scoped attribute` on CSS in Vue allows CSS to be scoped to a component only 

Vue has amazingly simple for SCSS SASS. To
install SASS:  

`npm install node-sass sass-loader --save-dev`  

Add to `*.vue` style tag

`<style lang="scss" scoped>`

## Mixins
Allows shared functionality to be pulled into a vue.js component. This can be used to share `computed`, `lifecycle events` anything really.

## Lifecycle hooks
### Created hook
Example: fetch data from an API when a component is rendered.
```
export default {
  name: 'RobotBuilder',
  created() {
    console.log('Shit went down');
  },
```    
Note: The `created` method is a convention for the hook!  
https://vuejs-tips.github.io/cheatsheet/#Options-Lifecycle-Hooks

## Inter-component communication
Child components must be referenced in both `import` and `components`    

```
import createdHookMixin from './created-hook-mixin';
import partsSelector from './PartsSelector.vue';

export default {
  name: 'RobotBuilder',
  components: [partsSelector],
  data() {
```
#### Props
`props` are simply properties on a component that allow us to pass things into it, via binding.  

```
export default {
  props: ['parts'],
```
##### Validating Props
Redefining props like this provides warning validations, that can include custom functions

```
  props: {
    parts: { type: Array, required: true },
    position: {
      type: String,
      required: true,
      validator: v => ['left', 'right', 'top'].includes(v),
    },
```
#### Events
Used to pass messages up to parent events.
`$emit` an event like so:

```
 methods: {
    selectNextPart() {
      this.selectedPartIndex = nextValidIndex(this.parts.length);
      this.$emit('partSelected', this.selectedPart);
    },
```
Then bind to it in the parent component with `v-on` a.k.a. `@`

``` 
<PartsSelector @partSelected="part => selectedRobot.head=part" .....
 ```
Sometimes it makes sense to emit a default state for child when rendering like so:

```
  created() {
    this.emitSelectedPart();  
    //  a wrapper for above  this.$emit('partSelected', this.selectedPart);
  },
 ```
 using the `updated` hook can make life easier at times, saving you intercepting too many events!

```
   updated() {
    this.emitSelectedPart();
  },
```

#### Slots
Slots can be used to push in external content into a control... collapsible menu's are an example.

### Routing
Install the vue-router:  
`npm install vue-router --save`

Define the router and routes:

```
import Vue from 'vue';
import Router from 'vue-router';

import HomePage from '../home/HomePage.vue';
import RobotBuilder from '../build/RobotBuilder.vue';

Vue.use(Router);

export default new Router({
  routes: [{
    path: '/',
    name: 'Home',
    component: HomePage,
  },
  {
    path: '/build',
    name: 'Build',
    component: RobotBuilder,
  }],
});
```

use vue `router-link`

```
<router-link to="/build">Get started</router-link> vue link<br />
```

or you can bind to a route name:

```
<router-link class="nav-link" :to="{name: 'Home'}">
```  

Register the router in in Main app

```
import Vue from 'vue';
import App from './App.vue';

import router from './router';

Vue.config.productionTip = false;

new Vue({
  render: h => h(App),
  router,
}).$mount('#app');
```
Routes can be called programatically  

```
 methods: {
    showPartInfo() {
      this.$router.push('/parts');
    },
```
#### HTML History Mode
Change router index.js to

```
export default new Router({
  mode: 'history',
  routes: [{
  ```
 To get HTML history mode to work well (i.e. supporting deep linking), you need to get you web server to serve the `vue` home index every time. Vue has some good docs for this.
 
 #### Navigation Route Guards
 The can proactively disable any routes that will not work. Added to a route  
   
```
 {
    path: '/parts/:partType/:id',
    name: 'Parts',
    component: PartInfo,
    props: true,
    beforeEnter(to, from, next) {
      const isValidId = Number.isInteger(Number(to.params.id));
      next(isValidId);
    }
```
#### Preventing users leaving pages with edits
##### Before leave route guard
On a component:

```
export default {
  name: 'RobotBuilder',
  beforeRouteLeave(to, from, next) {
    if (this.addedToCart) { next(true); } else {
      const response = confirm('You have not added you robot, are you crazy?!?');
      next(response);
    }
  },
```


## State with Vuex
* Properties must be added to state with sensible default, not added at run-time
* All changes must go through a mutation

##### Vuex Getters
For the derived state from the Store state

Mutators and getters in action

```
export default new Vuex.Store({
  state: {
    cart: [],
  },
  mutations: {
    addRobotToCart(state, robot) {
      state.cart.push(robot);
    },
  },
  getters: {
    cartSaleItems(state) {
      return state.cart.filter(item => item.head.onSale);
    },
  },
});
```

### Actions and asynch
using Axios for HTTP calls

View `created()` hook calls Axios, one once this returns, the store is updated and the computed value is updated, which in-turn updates the view. Add and `actions` section to the store as so.

```
  actions: {
    getParts({ commit }) {
      axios.get('/api/parts')
        .then(result => commit('updateParts', result.data))
        .catch(console.error);
    },
  },
```

vue enables a local proxy for API calls to deal with `cors` issues

```
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'http://locahost:8081',
        changeOrigin: true,
      },
    },
  },
};
```
#### Command Actions (Writing)
Use `dispatch` from the vue `method` and get it to return a promise.

```
 this.$store.dispatch('robots/addRobotToCart', Object.assign({}, robot, { cost }))
 .then(() => this.$router.push('/cart'));`
 ```

 The use axios to return promise and commit like so.

```
    addRobotToCart({ commit, state }, robot) {
      const cart = [...state.cart, robot];
      return axios.post('api/cart', cart)
        .then(() => commit('addRobotToCart', robot));
    },
```

### Splitting your store into modules 
#### Namespaced Modules
You can get vue to dispatch to multiple store events with one dispatch.. Nice! Feels like and event! Namespace modules will help prevent this.

`State` is always namespaced,  but `mutations`, `getters`, `actions` are only namespaced if you set  `namespaced: true,`


```
export default {
  namespaced: true,
```

After the namespace, the store dispatch must look like this:
`$store.dispatch('robots/robots/getParts);`  

In namespaced form, getter notation must change from dot notation to

```
    cartSaleItems() {
      return this.$store.getters['robots/cartSaleItems'];
```

### Global vs Namespaced State
**Root state**: is any state in the root store, as well as non-namespaced state objects in modules. The root namespace

* You can access your root state from namespaced store. It is the 3rd param named `rootState` on the getter method.
* Controversially, `rootState` is not available to `mutations` ,but but be accessed in `actions` via destructuring.

### Vuex Helper Functions
#### MapGetter and MapState
`mapState`, `mapGetter` can be used as convenience syntax for streamlining getters and actions.

```
import { mapState, mapGetters } from 'vuex';

export default {
  name: 'app',
  computed: {
    ...mapState({
      rootFoo: 'foo',
      robotFoo: state => state.robots.foo,
    }),
    cart() {
      return this.$store.state.robots.cart;
    },
    ...mapGetters({ rootGetterFoo: 'foo' }),
    ...mapGetters('robots', { robotsGetterFoo: 'foo' }),
  },
};
```

#### MapActions and MapMutations
`import { mapActions } from 'vuex';`
` ...mapActions('robots', ['getParts', 'addRobotsToCard'])`  
Allows this:  
`this.$store.dispatch('robots/getParts');`  
to become this:  
`this.getParts();`

*MapMutations* works the same!

#### Custom Directives
The v-pin example

` <span v-pin:position.top.right class="sale" v-show="selectedPart.onSale">Sale!</span>`

```
<script>
import pinDirective from '../shared/pin-directive';
```

`pin-directive.js`
You get  a `binding` param with `binding.arg = position` and the subsequent dot are a modifiers list.
```
export default {
  bind: (element, binding) => {
    if (binding.arg !== 'position') return;

    Object.keys(binding.modifiers).forEach((key) => {
      element.style[key] = '5px';
    });
    element.style.position = 'absolute';
  },
};
```

#### Binding syntax - alternative approach

```
    <span v-pin="{ bottom: '10px', right: '5px' }"
      class="sale" v-show="selectedPart.onSale">Sale!</span>
```

#### lifecycle hooks for directives

```
function applyStyle(element, binding) {........}

export default {
  bind: (element, binding) => {
    applyStyle(element, binding);
  },
  update: (element, binding) => {
    applyStyle(element, binding);
  },
  // Other possible lifecycle hooks
  inserted: {},
  componentUpdated: {},
  unbind: {},
};
```
#### The ultimate directive `bind` & `update` shortcut
A *very common* shortcut to bind to the update and bind lifecycle hook is:

```
export default function applyStyle(element, binding) {
  Object.keys(binding.value).forEach((position) => {
    element.style[position] = binding.value[position];
  });
  element.style.position = 'absolute';
}
```

### Global directive

In `main.js`, register a global directive.

```
import pinDirective from './shared/pin-directive';
........
Vue.directive('pin', pinDirective);
```

### Filters
* Filter does transformation, like a filter lens on a camera.
* Filters are just functions

##### `shoppingCart.vue`
```
{{robot.cost | currency('$')}}
.....
import currencyFilter from '../shared/currency-filter';
..... 
filters: {
    currency: currencyFilter,
  },
```
##### `currency-filter.js`

```
export default function (amount, symbol) {
  return `${symbol}${amount.toFixed(2)}`;
}
```
#### Global Filters
##### `main.js`
```
import currencyFilter from './shared/currency-filter';
Vue.filter('currency', currencyFilter);
```






  
  
    
 














