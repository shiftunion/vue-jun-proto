
# Vue Notes - Pluralsight - vuejs-fundamentals
https://app.pluralsight.com/library/courses/vuejs-fundamentals/table-of-contents
https://github.com/jmcooper/vuejs-fundamentals/tree/master/src

## CSS
`Scoped attribute` on CSS in Vue allows CSS to be scoped to a component only 

Vue has amazingly simple for SCSS SASS. To
install SASS:  
`npm install node-sass sass-loader --save-dev`  
Add to `*.vue` style tag  
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
Note: The `created` method is a convention for the hook!  
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









