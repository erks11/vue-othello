<template>
  <table id="othello">
    <tr>
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
      <th>e</th>
      <th>f</th>
      <th>g</th>
      <th>h</th>
    </tr>
    <tr v-for="(grids, i) in boardStatus" :key="i">
      <th>{{ i + 1 }}</th>
      <td
        v-for="(grid, k) in grids"
        v-bind:class="{ 'active': grid === -1 }"
        @click="$emit('setOnClickToGrid', grid,i,k)"
        :key="k"
      >
        <transition name="turn-over" mode="out-in">
          <div v-if="grid === 1" class="white-stone" key="white"></div>
          <div v-else-if="grid === 2" class="black-stone" key="black"></div>
        </transition>
      </td>
    </tr>
  </table>
</template>

<script>
export default {
  name: "Board",
  props: {
    boardStatus: Array,
    historyBoardStatus: Array
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#othello {
  border-collapse: collapse;
  background-color: #008080;
  border: 10px solid black;
  border-top-style: none;
  border-left-style: none;
}
th {
  background-color: black;
  color: blanchedalmond;
  font-size: xx-small;
}
#othello td {
  border: solid 1px black;
  width: 24px;
  height: 24px;
}
#othello td.active {
  background: lightseagreen;
}
#othello div {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  margin: 1px auto;
}
.turn-over-enter-active,
.turn-over-leave-active {
  transition: transform 1s linear 0s, background-color 0s linear 0.5s;
}
.turn-over-leave-to.black-stone {
  transform: rotateY(180deg);
  background-color: #fff8dc;
}
.turn-over-leave-to.white-stone {
  transform: rotateY(180deg);
  background-color: #000000;
}

div.black-stone {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: solid 1px #000000;
  background-color: #000000;
  margin: 1px auto;
}
div.white-stone {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: solid 1px #000000;
  background-color: #fff8dc;
  margin: 1px auto;
}
</style>
