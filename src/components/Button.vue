<template>
  <div
    v-on:click="$emit('btnClick', value) "
    :class="type"
    class="component btn"
  >{{visibleValue || value}}</div>
</template>

<script>
export default {
  props: {
    type: String,
    value: String,
    visibleValue: String
  }
};
</script>

<style scoped>
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

.input {
  background-color: white;
}

.operation {
  background-color: rgb(104, 103, 102);
  color: white;
}

.clear {
  background-color: rgb(243, 93, 93);
}

.equals {
  width: 172px;
}
</style>
