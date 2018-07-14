<template>
  <div class="content">
    <div class="preview">
      <CollapsibleSection :startOpen="true">

      <div class="preview-content">
        <div class="top-row">
          <img :src="selectedRobot.head.src"/>
        </div>
        <div class="middle-row">
          <img :src="selectedRobot.leftArm.src" class="rotate-left"/>
          <img :src="selectedRobot.torso.src"/>
          <img :src="selectedRobot.rightArm.src" class="rotate-right"/>
        </div>

        <div class="bottom-row">
          <img :src="selectedRobot.base.src"/>
        </div>

      </div>
      </CollapsibleSection>

      <CollapsibleSection></CollapsibleSection>

      <CollapsibleSection>
        <div>doing stuff</div>
      </CollapsibleSection>
      <button class="add-to-cart" @click="addToCart()">Add to Cart!</button>
    </div>


        <!-- A conditional style via a computed property -->
    <div class="top-row">
      <!-- <div class="robot-name">
          {{selectedRobot.head.title}}
          <span v-if="selectedRobot.head.onSale" class="sale">Sale!!!</span>
        </div> -->
       <PartsSelector @partSelected="part => selectedRobot.head=part"
         :parts="availableParts.heads"
         position="top" :class="[saleBorderClass, 'top-row']"/>
    </div>
    <div class="middle-row">
      <PartsSelector @partSelected="part => selectedRobot.leftArm=part"
        :parts="availableParts.arms"
        position="left" />
      <PartsSelector @partSelected="part => selectedRobot.torso=part"
        :parts="availableParts.torsos"
        position="center" />
      <PartsSelector @partSelected="part => selectedRobot.rightArm=part"
        :parts="availableParts.arms"
        position="right" />
    </div>
    <div class="bottom-row">
      <PartsSelector @partSelected="part => selectedRobot.base=part"
        :parts="availableParts.bases" position="bottom" />
    </div>
  </div>


</template>
<script>
import availableParts from '../data/parts';
import createdHookMixin from './created-hook-mixin';
import PartsSelector from './PartsSelector.vue';
import CollapsibleSection from '../shared/CollapsibleSection.vue';

/* eslint no-alert: 0 */
/* eslint no-restricted-globals: 0 */
export default {
  name: 'RobotBuilder',
  beforeRouteLeave(to, from, next) {
    if (this.addedToCart) { next(true); } else {
      const response = confirm('You have not added you robot, are you crazy?!?');
      next(response);
    }
  },
  components: { PartsSelector, CollapsibleSection },
  data() {
    return {
      cart: [],
      addedToCart: false,
      availableParts,
      selectedRobot: {
        head: {},
        leftArm: {},
        rightArm: {},
        torso: {},
        base: {},
      },
    };
  },
  /*
  Use computed properties as it's not a good idea to have complex methods in your template
  Why? A more simple template
  */
  computed: {
    saleBorderClass() {
      return this.selectedRobot.head.onSale ? 'sale-border' : '';
    },
  },
  mixins: [createdHookMixin],
  methods: {
    addToCart() {
      const robot = this.selectedRobot;
      const cost =
        robot.head.cost +
        robot.leftArm.cost +
        robot.rightArm.cost +
        robot.torso.cost +
        robot.base.cost;
      this.$store.commit('addRobotToCart', Object.assign({}, robot, { cost }));
      this.addedToCart = true;
    },
  },
};
</script>
<style lang="scss" scoped>
.part {
  position: relative;
  width: 165px;
  height: 165px;
  border: 3px solid #aaa;
}
.part {
  img {
    width: 165px;
  }
}
.top-row {
  display: flex;
  justify-content: space-around;
}
.middle-row {
  display: flex;
  justify-content: center;
}
.bottom-row {
  display: flex;
  justify-content: space-around;
  border-top: none;
}
.head {
  border-bottom: none;
}
.left {
  border-right: none;
}
.right {
  border-left: none;
}
.left img {
  transform: rotate(-90deg);
}
.right img {
  transform: rotate(90deg);
}
.bottom {
  border-top: none;
}
.prev-selector {
  position: absolute;
  z-index: 1;
  top: -3px;
  left: -28px;
  width: 25px;
  height: 171px;
}
.next-selector {
  position: absolute;
  z-index: 1;
  top: -3px;
  right: -28px;
  width: 25px;
  height: 171px;
}
.center .prev-selector,
.center .next-selector {
  opacity: 0.8;
}
.left .prev-selector {
  top: -28px;
  left: -3px;
  width: 144px;
  height: 25px;
}
.left .next-selector {
  top: auto;
  bottom: -28px;
  left: -3px;
  width: 144px;
  height: 25px;
}
.right .prev-selector {
  top: -28px;
  left: 24px;
  width: 144px;
  height: 25px;
}
.right .next-selector {
  top: auto;
  bottom: -28px;
  left: 24px;
  width: 144px;
  height: 25px;
}
.right .next-selector {
  right: -3px;
}
.robot-name {
  position: absolute;
  top: -25px;
  text-align: center;
  width: 100%;
}
.sale {
  color: red;
}
.content {
  position: relative;
}
.add-to-cart {
  position: absolute;
  width: 210px;
  padding: 3px;
  font-size: 16px;
}
.sale-border {
  border: 3px solid red;
}

.preview {
  position: absolute;
  top: -20px;
  right: 0;
  width: 210px;
  height: 210px;
  padding: 5px;
}
.preview-content {
  border: 1px solid #999;
}
.preview img {
  width: 50px;
  height: 50px;
}
.rotate-right {
  transform: rotate(90deg);
}
.rotate-left {
  transform: rotate(-90deg);
}

</style>

