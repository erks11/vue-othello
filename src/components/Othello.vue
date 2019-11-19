<template>
  <div id="tables">
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
      <tr v-for="(grids, i) in this.boardStatus" :key="i">
        <th>{{ i + 1 }}</th>
        <td
          v-for="(grid, k) in grids"
          v-bind:class="{'active': grid === -1}"
          @click="setOnClickToGrid(grid,i,k)"
          :key="k"
        >
          <div
            v-bind:class="{ 'black-stone': grid === 2 , 'white-stone': grid === 1, 'stone': grid > 0 }"
          ></div>
        </td>
      </tr>
    </table>

    <table id="battle-status">
      <tr>
        <td>○わたし</td>
      </tr>
      <tr>
        <td>
          <div
            v-bind:class="{'label-you': this.currentColor === 2, 'label-me': this.currentColor === 1}"
          ></div>
        </td>
      </tr>
      <tr>
        <td>{{ this.stoneCount }}</td>
      </tr>
      <tr>
        <td>{{ this.winnerText }}</td>
      </tr>
      <tr>
        <td>
          <button @click="reset()">reset</button>
        </td>
      </tr>
      <tr>
        <td>●あなた</td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  name: "Othello",
  data() {
    return {
      currentColor: 2,
      boardStatus: [
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 1, 2, 0, 0, 0],
        [0, 0, 0, 2, 1, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0]
      ],
      winner: 0,
      blackCount: 2,
      whiteCount: 2,
      stoneCount: "●:2 ○:2",
      winnerText: ""
    };
  },

  computed: {
    initialBoardStatus: function() {
      return [
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 1, 2, 0, 0, 0],
        [0, 0, 0, 2, 1, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0]
      ];
    },
    initialColor: function() {
      return 2;
    }
  },
  mounted() {
    this.init();
  },
  methods: {
    setOnClickToGrid(grid, i, k) {
      if (grid > 0) {
        return;
      }
      if (grid != -1) {
        return;
      }
      this.boardStatus[i][k] = this.currentColor;
      const result = this.executeOneTurn({ x: i, y: k });

      window.setTimeout(() => {
        this.executeCOMTurn(result.activeArr);
      }, 2000);
    },
    init() {
      this.boardStatus = this.initialBoardStatus;
      let res = this.judgeBoard();
      this.countStone(res.statusArr);
    },

    reset() {
      const boardStatus = [
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0]
      ];
      if (this.currentColor === this.initialColor) {
        this.boardStatus = boardStatus;
        this.judgeBoard();
        this.init();
      }
    },

    // コンピュータの１ターンの処理
    executeCOMTurn(activeArr) {
      // activeである-1の座標が入った配列を作る
      // その配列の中からランダムで、白が置く場所をきめる
      const cood =
        activeArr[Math.floor(Math.random() * (activeArr.length - 1))];

      // そこにおく
      this.boardStatus[cood.x][cood.y] = this.currentColor;
      // それを反映させる
      this.executeOneTurn(cood);
    },

    // オセロ１ターンの処理
    executeOneTurn(cood) {
      // ひっくり返す
      this.reverseStone({
        x: cood.x,
        y: cood.y
      });

      if (this.currentColor === 1) {
        this.currentColor = 2;
      } else {
        this.currentColor = 1;
      }
      // 次における場所があるかを判断する
      let result = this.judgeBoard();
      this.countStone();

      if (result.isContinue) {
        return result;
      }

      // pass判定
      let pass = 0;
      while (!result.isContinue) {
        if (this.currentColor === 1) {
          this.currentColor = 2;
        } else {
          this.currentColor = 1;
        }
        pass++;
        // passが２回だったらゲーム終了する
        if (pass >= 2) {
          this.finish();
          break;
        }
        result = this.judgeBoard();
      }
    },

    finish() {
      const res = this.countStone();
      // 勝敗を決める
      if (res.white === res.black) {
        this.winnerText = "引き分け";
      } else if (res.white > res.black) {
        this.winnerText = "わたしの勝ち";
      } else {
        this.winnerText = "あなたの勝ち";
      }
      this.currentColor = 0;
    },

    // 黒と白の石の数を数える
    countStone() {
      const statusArr = this.boardStatus;
      let whiteCount = 0;
      let blackCount = 0;
      for (let i = 0; i < statusArr.length; i++) {
        for (let k = 0; k < statusArr[i].length; k++) {
          if (statusArr[i][k] === 1) {
            whiteCount++;
          } else if (statusArr[i][k] === 2) {
            blackCount++;
          }
        }
      }
      this.stoneCount = "●:" + blackCount + " ○:" + whiteCount;
      return { white: whiteCount, black: blackCount };
    },

    reverseStone(cood) {
      let statusArrText = JSON.stringify(this.boardStatus);
      let reverseStatusArr = JSON.parse(statusArrText);
      // 置かれた石の周り８方向をみたい
      let resArr = this.judgeGridActive(reverseStatusArr, cood.x, cood.y);

      // searchOnewayでarrとxv,yvを受け取り、statusArrを変えてreturnしたい
      resArr.forEach(res => {
        // resArrのresultがtrueのものに対して、処理を行う
        // ここでx,yを定義し、多方向でひっくり返せるようにする
        let x = cood.x;
        let y = cood.y;
        if (res.result) {
          res.arr.forEach(i => {
            if (i != reverseStatusArr[x][y]) {
              x += res.xv;
              y += res.yv;
              reverseStatusArr[x][y] = reverseStatusArr[cood.x][cood.y];
            }
          });
        }
      });
      this.boardStatus = reverseStatusArr;
    },

    judgeBoard() {
      let statusArrText = JSON.stringify(this.boardStatus);
      let judgeStatusArr = JSON.parse(statusArrText);
      // 白が1 黒が2
      // 64マス全てに対して、置けるかどうか判断したい
      let isContinue = false;
      let activeArr = [];
      for (let i = 0; i < judgeStatusArr.length; i++) {
        for (let k = 0; k < judgeStatusArr[i].length; k++) {
          if (judgeStatusArr[i][k] > 0) {
            // すでに置いてあったらスルーする
            continue;
          }
          // いったんactiveを非表示にする
          if (judgeStatusArr[i][k] === -1) {
            judgeStatusArr[i][k] = 0;
          }

          // 置けるかどうか判断する
          // 置けるならactiveを表示
          let resArr = this.judgeGridActive(judgeStatusArr, i, k);
          // resArrのresultに一つでもtrueがあればそのtdをactiveにする
          resArr.forEach(res => {
            if (res.result) {
              activeArr.push({ x: i, y: k });
              isContinue = true;
              judgeStatusArr[i][k] = -1;
            }
          });
        }
      }
      this.boardStatus = judgeStatusArr;
      return {
        isContinue: isContinue,
        statusArr: judgeStatusArr,
        activeArr: activeArr
      };
    },

    // 周囲8マスをみてる
    judgeGridActive(statusArr, i, k) {
      let res;
      let resArr = [];
      for (let i2 = i - 1; i2 <= i + 1; i2++) {
        for (let k2 = k - 1; k2 <= k + 1; k2++) {
          // 自分自身なのでスルー
          if (i2 === i && k2 === k) {
            continue;
          }
          res = this.searchOneway(statusArr, { x: i, y: k }, i2 - i, k2 - k);
          resArr.push(res);
        }
      }
      return resArr;
    },

    searchOneway(statusArr, cood, xv, yv) {
      const mycolor = this.currentColor;
      let result = false;
      let arr = [];
      const x = cood.x;
      const y = cood.y;
      let new_cood = { x: x, y: y };
      while (
        new_cood.x < 8 &&
        new_cood.x >= 0 &&
        (new_cood.y < 8 && new_cood.y >= 0)
      ) {
        new_cood.x += xv;
        new_cood.y += yv;
        if (
          new_cood.x >= 0 &&
          new_cood.y >= 0 &&
          new_cood.x < 8 &&
          new_cood.y < 8
        ) {
          let stat = statusArr[new_cood.x][new_cood.y];
          arr.push(stat);
          // 隣が空白か、自分の色だったら抜ける
          if (stat <= 0 || stat == mycolor) {
            break;
          }
        }
      }
      // arr[0]が空白じゃなくて、自分の色でもない
      // arr[1]以上にずっと空白がないかつ自分の色がいる
      if (arr.length >= 2 && arr[arr.length - 1] === mycolor) {
        result = true;
      }
      return { arr: arr, xv: xv, yv: yv, result: result };
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
#tables {
  display: flex;
}
#othello {
  border-collapse: collapse;
  background-color: #008080;
  border: 10px solid black;
  border-top-style: none;
  border-left-style: none;
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
div.label-you::before {
  content: "あなたの番です";
}
div.label-me::before {
  content: "わたしの番です";
}
</style>
