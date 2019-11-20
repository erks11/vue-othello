<template>
  <table id="battle-status">
    <tr>
      <td>○わたし</td>
    </tr>
    <tr>
      <td>
        <div v-bind:class="{'label-you': currentColor === 2, 'label-me': currentColor === 1}"></div>
      </td>
    </tr>
    <tr>
      <td>●:{{ stoneCount.black }} ○:{{ stoneCount.white }}</td>
    </tr>
    <tr>
      <transition name="bounce">
        <td v-if="stoneCount.black === 2 && stoneCount.white === 2">スタート</td>
      </transition>
    </tr>
    <tr>
      <td>
        <button @click="$emit('reset')">reset</button>
      </td>
    </tr>
    <tr>
      <td>●あなた</td>
    </tr>
  </table>
</template>

<script>
export default {
  name: "GameStatus",
  props: {
    boardStatus: Array,
    currentColor: Number,
    stoneCount: Object,
    winnerText: String
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
#battle-status {
  background-color: lavender;
  width: 200px;
  height: 240px;
}
th {
  background-color: black;
  color: blanchedalmond;
  font-size: xx-small;
}
div.label-you::before {
  content: "あなたの番です";
}
div.label-me::before {
  content: "わたしの番です";
}
.bounce-enter-active {
  animation: bounce-in 0.5s;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(2);
  }
  100% {
    transform: scale(1);
  }
}
</style>
