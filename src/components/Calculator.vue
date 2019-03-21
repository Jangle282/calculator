<template>
  <div class="main">
    <h1>KALKULATOR</h1>
    <p>{{fact}}</p>
    <div class="calculator">
      <div :style="styleObject" class="component display">{{display || 0}}</div>
      <br>
      <div @click="clear" class="component btn clear">K</div>
      <div @click="plusMinus" class="component btn input">+/-</div>
      <div @click="operation(`/`)" class="component btn operator">/</div>
      <div @click="input(`7`)" class="component btn test input">7</div>
      <div @click="input(`8`)" class="component btn input">8</div>
      <div @click="input(`9`)" class="component btn input">9</div>
      <div @click="operation(`*`)" class="component btn operator">x</div>
      <div @click="input(`4`)" class="component btn input">4</div>
      <div @click="input(`5`)" class="component btn input">5</div>
      <div @click="input(`6`)" class="component btn input">6</div>
      <div @click="operation(`-`)" class="component btn operator">-</div>
      <div @click="input(`1`)" class="component btn input">1</div>
      <div @click="input(`2`)" class="component btn input">2</div>
      <div @click="input(`3`)" class="component btn input">3</div>
      <div @click="operation(`+`)" class="component btn operator">+</div>
      <div @click="input(`0`)" class="component btn input">0</div>
      <div @click="input(`.`)" class="component btn input">.</div>
      <div @click="operation(`start`)" class="component btn operator equals">=</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      runningCalc: "", // the running result of calculations performed
      display: "", // what is shown in the display div
      lastBtnType: "start", //  to determine logic for input and operator method can be "start" "input" or "operator"
      operator: "", // the last operator button clicked
      fact: "return a result to see a number fakt", // fact relating to the result from numbers api
      styleObject: {
        direction: "ltr"
      } // when display is too long for the screen can change language direction and clip on the left
    };
  },

  methods: {
    clear() {
      this.runningCalc = "";
      this.display = "";
      this.lastBtnType = "start";
      this.operator = "";
      this.fact = "Return a result to see a number fakt";
    },

    plusMinus() {
      this.display = String(parseInt(this.display) * -1);
    },

    input(value) {
      if (this.lastBtnType !== "input" && value != 0) {
        this.display = value;
      } else if (this.lastBtnType === "input" && this.display !== 0) {
        this.display += value;
      }
      if (this.display.length > 16) this.styleObject.direction = "rtl";
      this.lastBtnType = "input";
    },

    operation(value) {
      if (this.operator === "start") {
        this.runningCalc = this.display;
      } else {
        this.runningCalc = eval(
          `${this.runningCalc}${this.operator}${this.display}`
        );
      }
      if (value === "start") this.getFact(this.runningCalc);
      if (this.display.length > 16) {
        // this.runningCalc = String(parseInt(this.display).toExponential());
        this.styleObject.direction = "ltr";
      }
      this.display = this.runningCalc;
      this.operator = value;
      this.lastBtnType = "operator";
    },

    getFact(number) {
      number = Math.round(number) || 0;
      axios
        .get(`http://numbersapi.com/${number}?notfound=floor`)
        .then(fact => {
          console.log(fact);
          this.fact = fact.data;
        })
        .catch(error => console.log(error));
    }
  }
};
</script>

<style scoped>
.calculator {
  margin: 0 auto;
  padding: 60px 5px 40px;
  width: 400px;
  background-color: rgb(211, 206, 206);
  border-radius: 18px;
}

.component {
  display: inline-block;
  border-radius: 18px;
  cursor: default;
  user-select: none;
}

.btn {
  width: 80px;
  height: 80px;
  margin: 6px;
  line-height: 80px;
  font-size: 28px;
  box-shadow: 2px 2px rgb(75, 74, 74);
}

.btn:active {
  box-shadow: 1px 1px rgb(99, 98, 98);
  transform: translateY(1px);
  transform: translateX(1px);
}

.display {
  box-shadow: 0 0;
  width: 85%;
  height: 60px;
  padding: 5px;
  line-height: 60px;
  text-align: right;
  padding-right: 20px;
  margin-bottom: 15px;
  font-size: 34px;
  background-color: white;
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
}

.equals {
  width: 172px;
}

.operator {
  background-color: rgb(104, 103, 102);
  color: white;
}

.input {
  background-color: white;
}

.clear {
  width: 172px;
  background-color: rgb(243, 93, 93);
}
</style>
