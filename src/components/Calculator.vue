<template>
  <div class="main">
    <h1>KALKULATOR</h1>
    <h3>{{fact}}</h3>
    <div class="calculator">
      <div :style="styleObject" class="component display">
        <p>{{operator}}</p>
        {{display || 0}}
      </div>
      <br>
      <Button value="C" type="clear" v-on:btnClick="clear"/>
      <Button value="+/-" type="plusMinus" v-on:btnClick="plusMinus"/>
      <Button value="/" type="operation" v-on:btnClick="operation"/>
      <Button value="7" type="input" v-on:btnClick="input"/>
      <Button value="8" type="input" v-on:btnClick="input"/>
      <Button value="9" type="input" v-on:btnClick="input"/>
      <Button userValue="x" value="*" type="operation" v-on:btnClick="operation"/>
      <Button value="4" type="input" v-on:btnClick="input"/>
      <Button value="5" type="input" v-on:btnClick="input"/>
      <Button value="6" type="input" v-on:btnClick="input"/>
      <Button value="-" type="operation" v-on:btnClick="operation"/>
      <Button value="1" type="input" v-on:btnClick="input"/>
      <Button value="2" type="input" v-on:btnClick="input"/>
      <Button value="3" type="input" v-on:btnClick="input"/>
      <Button value="+" type="operation" v-on:btnClick="operation"/>
      <Button value="0" type="input" v-on:btnClick="input"/>
      <Button value="." type="input" v-on:btnClick="input"/>
      <Button userValue="=" value="ans" type="operation equals" v-on:btnClick="operation"/>
    </div>
  </div>
</template>

<script>
import Button from "./Button.vue";
import { setTimeout } from "timers";

export default {
  components: {
    Button
  },

  data() {
    return {
      runningCalc: "", // the running result of calculations performed inbetween clears.
      display: "", // what is shown in the display div
      lastBtnType: "ans", //  to determine logic for input and operator method. Can be "ans" "input" or "operator"
      operator: "", // the next operation to be performed on running calc
      fact: "Return a result to see a number fact", // fact relating to the result from numbers api
      styleObject: {
        direction: "ltr"
      } // when display is too long for the screen can change language direction and put ellipses on the left
    };
  },

  created: function() {
    window.addEventListener("keydown", this.keyDown);
  },

  methods: {
    clear() {
      this.keySound();
      this.runningCalc = "";
      this.display = "";
      this.lastBtnType = "ans";
      this.operator = "";
      this.fact = "Return a result to see a number fact";
    },

    plusMinus() {
      this.keySound();
      this.display = String(parseFloat(this.display) * -1);
      this.displayDir();
    },

    input(value) {
      this.keySound();
      if (this.lastBtnType !== "input" && value != 0) {
        this.display = " ";
        setTimeout(() => {
          this.display = value;
        }, 20); // to display a blink as user feedback that input has been received when the display could be same e.g. 3+3
      } else if (this.lastBtnType === "input" && this.display !== 0) {
        this.display += value;
      }
      this.displayDir();
      // if (this.display.length > 16) this.styleObject.direction = "rtl";
      this.lastBtnType = "input";
    },

    operation(value) {
      this.keySound();
      if (this.operator === "ans") {
        this.runningCalc = this.display; // for the first operator button press after a clear or equals
      } else if (this.lastBtnType === "operator") {
        this.operator = value; // if an operator is pushed more than once in a row - only changes the next operation to be performed.
      } else {
        this.runningCalc = eval(
          `${this.runningCalc}${this.operator}${this.display}`
        ); // calculates the running total according to the previous operator clicked and stores to runningCalc
      }
      this.display = this.runningCalc; // displays the runningCalc
      if (value === "ans") this.getFact(this.runningCalc); // retreives number fact for the displayed value if the button pressed was the equals button
      this.displayDir();
      this.operator = value; // sets the operator to the one the user just clicked
      this.lastBtnType = "operator"; //
    },

    // if the display number is long, converts it to an exponential and changes direction so can truncate at the left side of the display.
    displayDir() {
      if (this.display && this.display.length > 16) {
        this.display = String(parseFloat(this.display).toExponential());
        this.styleObject.direction = "rtl";
      } else {
        this.styleObject.direction = "ltr";
      }
    },

    // API call for number fact
    getFact(number) {
      number = Math.round(number) || 0;
      axios
        .get(`http://numbersapi.com/${number}?notfound=floor`)
        .then(fact => {
          this.fact = fact.data;
        })
        .catch(error => console.log(error));
    },

    // keyboard inputs
    keyDown(e) {
      if (this.operator === "ans" && e.key === "Backspace") this.clear();
      else {
        switch (e.key) {
          case "Enter":
            this.operation(`ans`);
            break;
          case "Backspace":
            this.display = this.display.slice(0, -1);
            break;
          case "1":
          case "2":
          case "3":
          case "4":
          case "5":
          case "6":
          case "7":
          case "8":
          case "9":
          case "0":
            this.input(e.key);
            break;
          case ".":
          case "=":
          case "+":
          case "*":
          case "/":
          case "-":
            this.operation(e.key);
            break;
          case "Delete":
          case "C":
          case "c":
            this.clear();
            break;
        }
      }
    },

    keySound() {
      var keyPress = new Audio("keyPress.mp3");
      keyPress.play();
    }
  }
};
</script>

<style>
.calculator {
  margin: 0 auto;
  padding: 60px 5px 40px;
  width: 400px;
  background-color: rgb(211, 206, 206);
  border-radius: 18px;
}

p {
  font-size: 14px;
  line-height: 0px;
  padding: 0;
}

.display {
  box-shadow: 0 0;
  width: 85%;
  height: 70px;
  padding: 5px;
  line-height: 40px;
  text-align: right;
  padding-right: 20px;
  margin-bottom: 15px;
  font-size: 34px;
  background-color: white;
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
}

.component {
  display: inline-block;
  border-radius: 18px;
  cursor: default;
  user-select: none;
}
</style>
