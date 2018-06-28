<template>
  <div class="content">
    <button class="add-to-cart" @click="addToCart()">Add to Cart!</button>
    <div class="top-row">
      <!-- A conditional style via a computed property -->
      <div class="top part" :class="[saleBorderClass, 'top','part']">

        <div class="robot-name">
          {{selectedRobot.head.title}}
          <span v-if="selectedRobot.head.onSale" class="sale">Sale!!!</span>
        </div>
        <img v-bind:src="selectedRobot.head.src" title="head" />
        <button v-on:click="selectPreviousHead()" class="prev-selector">&#9668;</button>
        <button v-on:click="selectNextHead()" class="next-selector">&#9658;</button>
      </div>
    </div>
    <div class="middle-row">
      <div class="left part">
        <img v-bind:src="selectedRobot.leftArm.src" title="left arm" />
        <button @click="selectNextLeftArm()" class="prev-selector">&#9650;</button>
        <button @click="selectPreviousLeftArm()" class="next-selector">&#9660;</button>
      </div>
      <div class="center part">
        <img v-bind:src="selectedRobot.torso.src" title="torso" />
        <button v-on:click="selectNextTorso()" class="prev-selector">&#9668;</button>
        <button v-on:click="selectPreviousTorso()" class="next-selector">&#9658;</button>
      </div>
      <div class="right part">
        <img :src="selectedRobot.rightArm.src" title="right arm" />
        <button @click="selectNextRightArm()" class="prev-selector">&#9650;</button>
        <button @click="selectPreviousRightArm()" class="next-selector">&#9660;</button>
      </div>
    </div>
    <div class="bottom-row">
      <div class="bottom part">
        <img :src="selectedRobot.base.src" title="base" />
        <button @click="selectNextBase()" class="prev-selector">&#9668;</button>
        <button @click="selectPreviousBase()" class="next-selector">&#9658;</button>
      </div>
    </div>
    <div>
      <h1>Cart</h1>
      <table>
        <thead>
          <tr>
            <th>Robot</th>
            <th>Cost</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(robot,index) in cart" :key="index">
            <td>{{robot.head.title}}</td>
            <td class="cost">{{robot.cost}}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>


</template>
<script>
import availableParts from '../data/parts';

const nextValidIndex = (index, maxIndex) => (index + 1) % maxIndex;
const previousValidIndex = (index, maxIndex) =>
  (maxIndex + index + 1) % maxIndex;

export default {
  name: 'RobotBuilder',
  data() {
    return {
      cart: [],
      availableParts,
      selectedHeadIndex: 0,
      selectedRightArmIndex: 0,
      selectedLeftArmIndex: 0,
      selectedTorsoIndex: 0,
      selectedBaseIndex: 0,
    };
  },
  /*
  Use computed properties as it's not a good idea to have complex methods in your template
  Why? A more simple template
  */
  computed: {
    selectedRobot() {
      return {
        head: availableParts.heads[this.selectedHeadIndex],
        leftArm: availableParts.arms[this.selectedLeftArmIndex],
        rightArm: availableParts.arms[this.selectedRightArmIndex],
        torso: availableParts.torsos[this.selectedTorsoIndex],
        base: availableParts.bases[this.selectedBaseIndex],
      };
    },
    saleBorderClass() {
      return this.selectedRobot.head.onSale ? 'sale-border' : '';
    },
  },
  methods: {
    addToCart() {
      const robot = this.selectedRobot;
      const cost =
        robot.head.cost +
        robot.leftArm.cost +
        robot.torso.cost +
        robot.base.cost;
      this.cart.push(Object.assign({}, robot, { cost })); // object assign  clones the object
    },
    selectNextHead() {
      this.selectedHeadIndex = nextValidIndex(
        this.selectedHeadIndex,
        availableParts.heads.length,
      );
    },
    selectPreviousHead() {
      this.selectedHeadIndex = previousValidIndex(
        this.selectedHeadIndex,
        availableParts.heads.length,
      );
    },
    selectNextRightArm() {
      this.selectedRightArmIndex = nextValidIndex(
        this.selectedRightArmIndex,
        availableParts.arms.length,
      );
    },
    selectPreviousRightArm() {
      this.selectedRightArmIndex = previousValidIndex(
        this.selectedRightArmIndex,
        availableParts.arms.length,
      );
    },
    selectNextLeftArm() {
      this.selectedLeftArmIndex = nextValidIndex(
        this.selectedLeftArmIndex,
        availableParts.arms.length,
      );
    },
    selectPreviousLeftArm() {
      this.selectedLeftArmIndex = previousValidIndex(
        this.selectedLeftArmIndex,
        availableParts.arms.length,
      );
    },
    selectNextBase() {
      this.selectedBaseIndex = nextValidIndex(
        this.selectedBaseIndex,
        availableParts.bases.length,
      );
    },
    selectPreviousBase() {
      this.selectedBaseIndex = previousValidIndex(
        this.selectedBaseIndex,
        availableParts.bases.length,
      );
    },
    selectNextTorso() {
      this.selectedTorsoIndex = nextValidIndex(
        this.selectedTorsoIndex,
        availableParts.torsos.length,
      );
    },
    selectPreviousTorso() {
      this.selectedTorsoIndex = previousValidIndex(
        this.selectedTorsoIndex,
        availableParts.torsos.length,
      );
    },
  },
};
</script>
<style scoped>
.part {
  position: relative;
  width: 165px;
  height: 165px;
  border: 3px solid #aaa;
}
.part img {
  width: 165px;
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
  right: 30px;
  width: 220px;
  padding: 3px;
  font-size: 16px;
}
td,
th {
  text-align: left;
  padding: 5px;
  padding-right: 20px;
}
.cost {
  text-align: right;
}
.sale-border
{
  border: 3px solid red;
}
</style>

