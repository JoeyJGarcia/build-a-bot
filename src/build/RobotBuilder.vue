<template>
  <div class="content">
    <button class="add-to-cart" @click="addToCart()">Add To Cart</button>
    <div class="top-row">
      <div :class="[saleBorderClass, 'top', 'part']">
        <div class="robot-name">{{selectedRobot.head.title}}
          <span v-if="selectedRobot.head.onSale" class="sale">Sale!</span>
        </div>
        <img :src="selectedRobot.head.src" title="head"/>
        <button @click="selectPrevious('Heads')"
        class="prev-selector">&#9668;</button>
        <button @click="selectNext('Heads')" class="next-selector">&#9658;</button>
      </div>
    </div>
    <div class="middle-row">
      <div class="left part">
        <img :src="selectedRobot.leftArm.src" title="left arm"/>
        <button @click="selectPrevious('Arms', 'Left')"
        class="prev-selector">&#9650;</button>
        <button @click="selectNext('Arms', 'Left')" class="next-selector">&#9660;</button>
      </div>
      <div class="center part">
        <img :src="selectedRobot.torso.src" title="left arm"/>
        <button @click="selectPrevious('Torsos')"
        class="prev-selector">&#9668;</button>
        <button @click="selectNext('Torsos')" class="next-selector">&#9658;</button>
      </div>
      <div class="right part">
        <img :src="selectedRobot.rightArm.src" title="left arm"/>
        <button @click="selectPrevious('Arms', 'Right')"
        class="prev-selector">&#9650;</button>
        <button @click="selectNext('Arms', 'Right')" class="next-selector">&#9660;</button>
      </div>
    </div>
    <div class="bottom-row">
      <div class="bottom part">
        <img :src="selectedRobot.base.src" title="left arm"/>
        <button @click="selectPrevious('Bases')"
        class="prev-selector">&#9668;</button>
        <button @click="selectNext('Bases')" class="next-selector">&#9658;</button>
      </div>
    </div>
    <div>
      <h1>Cart</h1>
      <table>
        <thead>
          <tr>
            <th>Robot</th>
            <th class="cost">Cost</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(robot, index) in cart" :key="index">
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
import createdHookMixin from './created-hook-mixin';

export default {
  name: 'RobotBuilder',
  data() {
    return {
      availableParts,
      selectedHeadsIndex: 0,
      selectedLeftArmsIndex: 0,
      selectedRightArmsIndex: 0,
      selectedTorsosIndex: 0,
      selectedBasesIndex: 0,
      cart: [],
    };
  },
  mixins: [createdHookMixin],
  computed: {
    saleBorderClass() {
      return this.selectedRobot.head.onSale ? 'sale-border' : '';
    },
    headBorderStyle() {
      return {
        border: this.selectedRobot.head.onSale
          ? '3px solid red'
          : '3px solid #aaa',
      };
    },
    selectedRobot() {
      return {
        head: availableParts.heads[this.selectedHeadsIndex],
        leftArm: availableParts.arms[this.selectedLeftArmsIndex],
        torso: availableParts.torsos[this.selectedTorsosIndex],
        rightArm: availableParts.arms[this.selectedRightArmsIndex],
        base: availableParts.bases[this.selectedBasesIndex],
      };
    },
  },
  methods: {
    addToCart() {
      const robot = this.selectedRobot;
      const cost = robot.head.cost
        + robot.leftArm.cost
        + robot.torso.cost
        + robot.rightArm.cost
        + robot.base.cost;
      this.cart.push({ ...robot, ...{ cost } });
      console.log(this.cart[this.cart.length - 1]);
    },
    decrementIndex(idx, boundary, resetValue) {
      console.log(`${idx}, ${boundary}, ${resetValue}`);
      return idx - 1 < boundary ? resetValue : idx - 1;
    },
    incrementIndex(idx, boundary, resetValue) {
      console.log(`${idx}, ${boundary}, ${resetValue}`);
      return idx + 1 > boundary ? resetValue : idx + 1;
    },
    selectNext(part, side = '') {
      console.log(`Next ${part}`);
      const partIdx = this[`selected${side}${part}Index`];
      const boundary = this.availableParts[`${part.toLowerCase()}`].length - 1;
      const reset = 0;
      this[`selected${side}${part}Index`] = this.incrementIndex(partIdx, boundary, reset);
    },
    selectPrevious(part, side = '') {
      console.log(`Previous ${part}`);
      const partIdx = this[`selected${side}${part}Index`];
      const boundary = 0;
      const reset = this.availableParts[`${part.toLowerCase()}`].length - 1;
      this[`selected${side}${part}Index`] = this.decrementIndex(partIdx, boundary, reset);
    },
  },
};
</script>

<style lang="scss" scoped>
.part {
  position: relative;
  width:165px;
  height:165px;
  border: 3px solid #aaa;
}
.part {
  img {
    width:165px;
  }
}
.top-row {
  display:flex;
  justify-content: space-around;
}
.middle-row {
  display:flex;
  justify-content: center;
}
.bottom-row {
  display:flex;
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
  z-index:1;
  top: -3px;
  left: -28px;
  width: 25px;
  height: 171px;
}
.next-selector {
  position: absolute;
  z-index:1;
  top: -3px;
  right: -28px;
  width: 25px;
  height: 171px;
}
.center .prev-selector, .center .next-selector {
  opacity:0.8;
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
  width: 50px;
  background-color: yellow;
  color: tomato;
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

td, th {
  text-align:left;
  padding: 5px;
  padding-right: 20px;
}

.cost {
  text-align: right;
}

.sale-border {
  border: 3px solid red;
}
</style>
