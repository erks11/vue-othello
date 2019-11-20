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
        <div
          v-bind:class="{ 'black-stone': grid === 2 , 'white-stone': grid === 1, 
            'stone': isStone(i,k) }"
        ></div>
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
  },
  methods: {
    isStone(i, k) {
      const len = this.historyBoardStatus.length;
      return len > 2 && this.historyBoardStatus[len - 2][i][k] > 0;
    }
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
div.stone {
  transition: transform 1s linear 0s, background-color 0s linear 0.5s;
}
div.black-stone {
  transform: rotateY(180deg);
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: solid 1px #000000;
  background-color: #000000;
  margin: 1px auto;
}
div.white-stone {
  transform: rotateY(360deg);
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: solid 1px #000000;
  background-color: #fff8dc;
  margin: 1px auto;
}
</style>
