<template>
  <div class="main">
    <h1>KALKULATOR</h1>
    <p>{{fact}}</p>
    <div class="calculator">
      <div :style="styleObject" class="component display">
        <p>{{operator}}</p>
        {{display || 0}}
      </div>
      <br>
      <div @click="clear" class="component btn clear">C</div>
      <div @click="plusMinus" class="component btn input">+/-</div>
      <div @click="operation(`/`)" class="component btn operator">/</div>
      <div @click="input(`7`)" class="component btn input">7</div>
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
      <div @click="operation(`ans`)" class="component btn operator equals">=</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      runningCalc: "", // the running result of calculations performed inbetween clears or new anss.
      display: "", // what is shown in the display div
      lastBtnType: "ans", //  to determine logic for input and operator method can be "ans" "input" or "operator"
      operator: "", // the last operator button clicked
      fact: "Return a result to see a number fact", // fact relating to the result from numbers api
      styleObject: {
        direction: "ltr"
      } // when display is too long for the screen can change language direction and clip on the left
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
        this.display = value;
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
        this.runningCalc = this.display; // for the first operator button press after a clear
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
  box-shadow: 3px 3px rgb(75, 74, 74);
}

.btn:active {
  box-shadow: 1px 1px rgb(99, 98, 98);
  transform: translateY(1px);
  transform: translateX(1px);
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
