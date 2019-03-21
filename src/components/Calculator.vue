<template>
  <div class="main">
    <h1>KALKULATOR</h1>
    <div class="calculator">
      <div class="btn display">{{current || 0}}</div>
      <br>
      <div @click="clear" class="btn clear">C</div>
      <div class="btn input">+/-</div>
      <div @click="append(`/`)" class="btn operator">/</div>
      <div @click="append(7)" class="btn input">7</div>
      <div @click="append(8)" class="btn input">8</div>
      <div @click="append(9)" class="btn input">9</div>
      <div @click="append(`*`)" class="btn operator">x</div>
      <div @click="append(4)" class="btn input">4</div>
      <div @click="append(5)" class="btn input">5</div>
      <div @click="append(6)" class="btn input">6</div>
      <div @click="append(`-`)" class="btn operator">-</div>
      <div @click="append(1)" class="btn input">1</div>
      <div @click="append(2)" class="btn input">2</div>
      <div @click="append(3)" class="btn input">3</div>
      <div @click="append(`+`)" class="btn operator">+</div>
      <div @click="append(0)" class="btn input">0</div>
      <div @click="append(`.`)" class="btn input">.</div>
      <div @click="calculate" class="btn operator equals">=</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      current: ""
    };
  },
  methods: {
    clear() {
      this.current = "";
    },
    clearAll() {
      this.accumulator = "";
      this.current = "";
    },
    append(value) {
      this.current += value;
      // maximum length
      // can't put zero at teh front
    },

    plusMinus() {
      // don't need if showing string in array
      this.current = `-${this.current}`;
    },

    calculate() {
      this.current = eval(this.current);
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

.btn {
  width: 80px;
  height: 80px;
  display: inline-block;
  margin: 6px;
  border-radius: 18px;
  line-height: 80px;
  font-size: 28px;
}

.display {
  width: 85%;
  height: 60px;
  padding: 5px;
  line-height: 60px;
  text-align: right;
  padding-right: 20px;
  margin-bottom: 15px;
  font-size: 34px;
  background-color: white;
}
.equals {
  width: 172px;
}

.operator {
  background-color: rgb(65, 58, 47);
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
