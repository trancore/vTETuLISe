<template>
  <div id="app">
    <div class="display">
      <div class="field">
        <div class="left">
          <div class="queue holdQueue">
            <table class="holdQueueTable">
              <tr class="holdQueueTr">
                <td class="holdBlock holdQueueTd" />
                <td class="holdBlock holdQueueTd" />
                <td class="holdBlock holdQueueTd" />
                <td class="holdBlock holdQueueTd" />
                <td class="holdBlock holdQueueTd" />
              </tr>
              <tr
                v-for="(line, i) in displayHoldBlock"
                :key="i"
                class="holdQueueTr"
              >
                <td
                  v-for="(cell, j) in line"
                  :key="j"
                  class="holdBlock holdQueueTd"
                  :class="cell | blockClass"
                />
              </tr>
            </table>
          </div>
          <div class="gameInformation">
            <ul>
              <div class="box playerBox">
                <li class="playerNameTitle">
                  PLAYER-NAME
                </li>
                <li class="scoreData playerName">
                  player-name
                </li>
                <li class="gameTypeTitle">
                  GAME-TYPE
                </li>
                <li class="scoreData gameType">
                  NORMAL
                </li>
              </div>
              <div class="box scoreBox">
                <li class="scoreTitle">
                  SCORE
                </li>
                <li class="scoreData scoreCount">
                  {{ displayScore }}
                </li>
              </div>
              <div class="box timeBox">
                <li class="timeTitle">
                  TIME
                </li>
                <li class="scoreData timeCount">
                  {{ checkHours | zeroPadding }}：{{ checkMinutes | zeroPadding }}：{{ checkSeconds | zeroPadding }}
                </li>
              </div>
              <div class="box lineBox">
                <li class="lineTitle">
                  LINE:
                </li>
                <li class="scoreData linesCount">
                  {{ scoreData.deleteLines }}
                </li>
              </div>
              <div class="box levelBox">
                <li class="levelTitle">
                  LEVEL:
                </li>
                <li class="scoreData levelCount">
                  {{ scoreData.level }}
                </li>
              </div>
              <div class="box tetliseBox">
                <li class="tetriseTitle">
                  TETRISES:
                </li>
                <li class="scoreData tetrisesCount">
                  00
                </li>
              </div>
            </ul>
          </div>
        </div>
        <div class="center">
          <div class="matrix">
            <table>
              <tr
                v-for="(line, i) in displayField"
                :key="i"
              >
                <!-- mustaches展開 -->
                <td
                  v-for="(cell, j) in line"
                  :key="j"
                  class="block"
                  :class="cell | blockClass"
                />
              </tr>
            </table>
          </div>
          <button @click="start">
            start
          </button>
        </div>
        <div class="right">
          <div class="queue nextQueue">
            <table class="nextQueueTable">
              <tr class="nextQueueTr">
                <td class="nextBlock nextQueueTd" />
                <td class="nextBlock nextQueueTd" />
                <td class="nextBlock nextQueueTd" />
                <td class="nextBlock nextQueueTd" />
                <td class="nextBlock nextQueueTd" />
              </tr>
              <tr
                v-for="(line, i) in displayNextBlock"
                :key="i"
                class="nextQueueTr"
              >
                <td
                  v-for="(cell, j) in line"
                  :key="j"
                  class="nextBlock nextQueueTd"
                  :class="cell | blockClass"
                />
              </tr>
            </table>
          </div>
          <div class="queue afterQueue">
            afterQueue
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// テトリミノ
const tetrimino = {
  0: [
    [0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0]
  ],
  // I Tetrimino
  1: [
    [0, 0, 1, 0, 0],
    [0, 0, 1, 0, 0],
    [0, 0, 1, 0, 0],
    [0, 0, 1, 0, 0],
    [0, 0, 0, 0, 0]
  ],
  // O Tetrimino
  2: [
    [0, 0, 0, 0, 0],
    [0, 0, 2, 2, 0],
    [0, 0, 2, 2, 0],
    [0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0]
  ],
  // T Tetrimino
  3: [
    [0, 0, 0, 0, 0],
    [0, 0, 3, 0, 0],
    [0, 3, 3, 3, 0],
    [0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0]
  ],
  // J Tetrimino
  4: [
    [0, 0, 0, 0, 0],
    [0, 0, 4, 4, 0],
    [0, 0, 4, 0, 0],
    [0, 0, 4, 0, 0],
    [0, 0, 0, 0, 0]
  ],
  // L Tetrimino
  5: [
    [0, 0, 0, 0, 0],
    [0, 0, 5, 0, 0],
    [0, 0, 5, 0, 0],
    [0, 0, 5, 5, 0],
    [0, 0, 0, 0, 0]
  ],
  // S Tetrimino
  6: [
    [0, 0, 0, 0, 0],
    [0, 0, 6, 6, 0],
    [0, 6, 6, 0, 0],
    [0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0]
  ],
  // Z Tetrimino
  7: [
    [0, 0, 0, 0, 0],
    [0, 7, 7, 0, 0],
    [0, 0, 7, 7, 0],
    [0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0]
  ]
};
export default {
  filters: {
    /**
     * テトリミノの種類判別クラス付与
     * @param  val テトリミノタイプ
     * @return テトリミノクラス
     */
    blockClass(val) {
      switch (val) {
        case 1:
          return 'tetrimino-i';
        case 2:
          return 'tetrimino-o';
        case 3:
          return 'tetrimino-t';
        case 4:
          return 'tetrimino-j';
        case 5:
          return 'tetrimino-l';
        case 6:
          return 'tetrimino-s';
        case 7:
          return 'tetrimino-z';
        default:
          return '';
      }
    },
    /**
     * プレイ時間のゼロ埋め
     * @param  value 時間
     * @return ゼロ埋めした時間
     */
    zeroPadding(value) {
      return value.toString().padStart(2, 0);
    }
  },
  data: function() {
    return {
      field: {
        data: [],
        x: 10,
        y: 20
      },
      block: {
        // ブロックタイプ
        type: 1,
        data: [],
        // x軸座標
        x: 0,
        // y軸座標
        y: 0
      },
      nextBlock: {
        type: 0
      },
      holdBlock: {
        type: 0
      },
      intervalId: undefined,
      player: {
        name: '',
        gameType: ''
      },
      scoreData: {
        score: 0,
        deleteLines: 0,
        level: 1,
        tetris: 0
      },
      time: {
        playTime: 0,
        endTime: 0,
        diffTime: 0,
        intervalId: 0,
        isRunning: false
      }
    };
  },
  computed: {
    /**
     * フィールド画面の表示
     */
    displayField() {
      // ボードのコピー
      const field = JSON.parse(JSON.stringify(this.field.data));
      if (this.block.data.length === 0) {
        return field;
      }
      // ブロックの描画switch
      for (let h = 0; h < this.block.data.length; h++) {
        for (let v = 0; v < this.block.data[h].length; v++) {
          const y = this.block.y + h;
          const x = this.block.x + v;
          if (y < 0 || x < 0 || y > this.field.y - 1 || x > this.field.x - 1) {
            continue;
          }
          if (this.block.data[h][v] === 0) {
            continue;
          }
          field[h + this.block.y][v + this.block.x] = this.block.data[h][v];
        }
      }
      return field;
    },
    /**
     * 次のテトリミノの表示
     * @return ホールドしたテトリミノのタイプ
     */
    displayNextBlock() {
      return tetrimino[this.nextBlock.type];
    },
    /**
     * ホールドしたテトリミノの表示
     * @return ホールドしたテトリミノのタイプ
     */
    displayHoldBlock() {
      return tetrimino[this.holdBlock.type];
    },
    /**
     * スコアの表示
     * @return スコア
     */
    displayScore() {
      return this.scoreData.score;
    },
    /**
     * '時'の表示
     * @return 時
     */
    checkHours() {
      return Math.floor(this.time.diffTime / 1000 / 60 / 60);
    },
    /**
     * '分'の表示
     * @return 分
     */
    checkMinutes() {
      return Math.floor(this.time.diffTime / 1000 / 60) % 60;
    },
    /**
     * '秒'の表示
     * @return 秒
     */
    checkSeconds() {
      return Math.floor(this.time.diffTime / 1000) % 60;
    },
  },
  // ライフサイクル
  created() {
    this.clear();
  },
  mounted() {
    window.addEventListener('keydown', this.handleKeydown);
  },
  beforeDestroy() {
    window.removeEventListener('keydown', this.handleKeydown);
  },
  // メソッド
  methods: {
    /**
     * 初期化
     */
    clear() {
      this.field.data = [...Array(this.field.y)].map(() => Array(this.field.x).fill(0));
      this.stopDropDown();
    },
    /**
     * ゲームの開始
     */
    start() {
      this.clear();
      this.block.type = this.getRandomBlock();
      this.nextBlock.type = this.getRandomBlock();
      this.setBlock();
      this.timeStart();
      this.dropDown();
      document.activeElement.blur();
    },
    /**
     * テトリミノの表示
     */
    setBlock() {
      this.block.x = 2;
      this.block.y = this.block.type === 1 ? 0 : -1;
      this.block.data = JSON.parse(JSON.stringify(tetrimino[this.block.type]));
    },
    /**
     * 移動可否判定
     * @param  Array block アクティブテトリミノ
     * @param  int   x     アクティブテトリミノのx座標
     * @param  int   y     アクティブテトリミノのy座標
     * @return false       移動不可
     */
    canMove(block, x, y) {
      for (let h = 0; h < block.length; h++) {
        for (let v = 0; v < block[h].length; v++) {
          //左端判定
          if (x + v < 0 && block[h][v] > 0) {
            return false;
          }
          //右端判定
          if (x + v > this.field.x - 1 && block[h][v] > 0) {
            return false;
          }
          //下端判定
          if (y + h > this.field.y - 1 && block[h][v] > 0) {
            return false;
          }
          //上端判定
          if (y + h < 0 && block[h][v] > 0) {
            return false;
          }
          //ボード外の座標は無視
          if (x + v < 0 || x + v > this.field.x - 1 || y + h > this.field.y - 1 || y + h < 0) {
            continue;
          }
          //ブロック判定
          if (this.field.data[y + h][x + v] > 0 && block[h][v] > 0) {
            return false;
          }
        }
      }
      return true;
    },
    /**
     * 右移動
     */
    right() {
      if (!this.canMove(this.block.data, this.block.x + 1, this.block.y)) {
        return;
      }
      this.block.x += 1;
    },
    /**
     * 左移動
     */
    left() {
      if (!this.canMove(this.block.data, this.block.x - 1, this.block.y)) {
        return;
      }
      this.block.x -= 1;
    },
    /**
     * 下移動（ソフトドロップ）
     */
    softDrop() {
      if (this.canMove(this.block.data, this.block.x, this.block.y + 1)) {
        this.block.y += 1;
        this.stopDropDown();
        this.dropDown();
        return;
      }
      this.updateField();
    },
    /**
     * 最下移動（ハードドロップ）
     */
    hardDrop() {
      while (this.canMove(this.block.data, this.block.x, this.block.y + 1)) {
        this.softDrop();
      }
      this.updateField();
    },
    /**
     * 回転
     */
    rotate() {
      // O型は回転しない
      if (this.block.type === 2) {
        return;
      }

      // 回転後のブロック生成
      const rotatedTetrimino = JSON.parse(JSON.stringify(this.block.data));
      for (let h = 0; h < rotatedTetrimino.length; h++) {
        for (let v = 0; v < rotatedTetrimino[h].length; v++) {
          rotatedTetrimino[rotatedTetrimino.length - v - 1][h] = this.block.data[h][v];
        }
      }

      // 回転可否判定
      if (!this.canMove(rotatedTetrimino, this.block.x, this.block.y)) {
        return;
      }

      this.block.data = rotatedTetrimino;
    },
    /**
     * キー設定
     * @param event イベント
     */
    handleKeydown(event) {
      // 最下移動（space）
      if (event.keyCode === 32) {
        this.hardDrop();
      }
      // 左移動（←）
      else if (event.keyCode === 37) {
        this.left();
      }
      // 回転移動（↑）
      else if (event.keyCode === 38) {
        this.rotate();
      }
      // 右移動（→）
      else if (event.keyCode === 39) {
        this.right();
      }
      // 下移動（↓）
      else if (event.keyCode === 40) {
        this.softDrop();
      }
    },
    /**
     * 自動落下
     * 一定間隔ごとにメソッドを実行
     */
    dropDown() {
      this.intervalId = setInterval(this.softDrop, 1000 * Math.pow((0.8 - (this.scoreData.level - 1) * 0.007), (this.scoreData.level - 1)));
    },
    /**
     * 自動落下の停止
     */
    stopDropDown() {
      clearInterval(this.intervalId);
    },
    /**
     * 次のテトリミノの設定
     */
    setNext() {
      this.block.type = this.nextBlock.type;
      this.nextBlock.type = this.getRandomBlock();
    },
    /**
     * 次のテトリミノの取得
     * @return 次のテトリミノのタイプ
     */
    getNextBlock() {
      return tetrimino[this.nextBlock.type];
    },
    /**
     * テトリミノの配置
     */
    setTetorimino() {
      // 次のテトリミノの設定
      this.setNext();
      this.setBlock();
    },
    /**
     * フィールドの更新
     */
    updateField() {
      this.stopDropDown();
      // 最下端にたどり着いたら、フィールドの更新とIntervalIDの破棄
      this.field.data = JSON.parse(JSON.stringify(this.displayField));
      // ラインの削除
      const deleteLines = this.deleteLine();
      // ゲームスコアの更新
      this.updateScore(deleteLines);
      // 次のテトリミノを配置
      this.setTetorimino();
      this.dropDown();
    },
    // テトリミノのランダム取得
    getRandomBlock() {
      return Math.floor(Math.random() * 7) + 1;
    },
    /**
     * ラインの消滅
     * @param  field フィールド
     * @param  yLine lineのy軸座標
     */
    disappeal(field, yLine) {
      // 一瞬で消える
      for (let x = 0; x < this.field.x; x++) {
        this.field.data[yLine][x] = 0;
      }
    },
    /**
     * ラインの表示
     * @param  appealField 削除前フィールド
     */
    appeal(appealField) {
      this.field = appealField;
    },
    // ラインの点滅
    flash(field, yLine) {
      const appealField = field;
      this.disappeal(field, yLine);
      setTimeout(() => {
        this.appeal(appealField);
      }, 5000);
      setTimeout(() => {
        this.disappeal(field, yLine);
      }, 2000);
    },
    /**
     * 一段下げる
     * @param yLine lineのy軸座標
     */
    downLine(yLine) {
      for (let h = yLine; h > 1; h--) {
        this.field.data[h] = this.field.data[h - 1];
      }
    },
    /**
     * ラインの削除
     */
    deleteLine() {
      //ライン消し判定
      const yLineList = [];
      for (let y = 0; y < this.field.y; y++) {
        let c = 1;
        for (let x = 0; x < this.field.x; x++) {
          c *= this.field.data[y][x];
        }
        if (c > 0) {
          yLineList.push(y);
        }
      }
      //ライン消し
      for (let i = 0; i < yLineList.length; i++) {
        const yLine = yLineList[i];
        // TODO 点滅処理
        // this.flash(this.field, yLine);
        this.downLine(yLine);
      }
      // 削除するライン数の加算
      this.scoreData.deleteLines += yLineList.length;
      return yLineList;
    },
    /**
     * スコアの取得
     * @param  deleteLines 削除するライン
     */
    getScore(deleteLines) {
      switch (deleteLines.length) {
        case 1:
          this.scoreData.score += 100 * this.scoreData.level;
          break;
        case 2:
          this.scoreData.score += 300 * this.scoreData.level;
          break;
        case 3:
          this.scoreData.score += 500 * this.scoreData.level;
          break;
        case 4:
          this.scoreData.score += 800 * this.scoreData.level;
          break;
        default:
          break;
      }
    },
    /**
     * タイマースタート
     * @param time 時間
     */
    setStartTime(time) {
      this.time.playTime = performance.now() - time;
    },
    /**
     * プレイ時間
     */
    progressTime() {
      this.time.endTime = performance.now();
      this.time.diffTime = this.time.endTime - this.time.playTime;
    },
    /**
     * プレイ時間の計測開始
     */
    timeStart() {
      if (this.time.isRunning) {
        return false;
      }
      this.time.isRunning = true;
      this.setStartTime(this.time.diffTime);
      this.time.intervalId = setInterval(this.progressTime, 1000);
    },
    updateLevel() {
      if (this.scoreData.deleteLines > (this.scoreData.level + 1) * 10) {
        this.scoreData.level += 1;
      }
      return;
    },
    /**
     * スコア情報の更新
     */
    updateScore(deleteLines) {
      // SCOREの更新
      this.getScore(deleteLines);
      // LINEの更新
      // LEVELの更新
      this.updateLevel();
      // TETRISの更新
    }
  }
};
</script>

<style>
:root {
  --field-block: calc(100vh * 0.045);
  --next-block: calc(100vh * 0.035);
}

ul {
  padding-left: 0;
  border-left: 3px #626261 solid;
}

li {
  margin-left: 8px;
  list-style: none;
}

table {
  border: 5px #626261 solid;
  border-collapse: collapse;
}

table.nextQueueTable,
table.holdQueueTable {
  border: 7px #626261 solid;
  border-collapse: collapse;
}

tr,
td {
  border: 1px #626261 solid;
  height: var(--field-block);
  min-width: var(--field-block);
}

tr.nextQueueTr,
td.nextQueueTd,
tr.holdQueueTr,
td.holdQueueTd {
  border: none;
  height: var(--next-block);
  min-width: var(--next-block);
}

.display {
  height: 100vh;
  width: 100vw;
  background-color: #B2B3B2;
}

.field {
  display: flex;
  justify-content: center;
  width: 80%;
  margin: auto;
  padding-top: 20px;
}

.left {
  margin-right: 10px;
}

.block {
  width: var(--field-block);
  height: var(--field-block);
  background-color: white;
}

.nextBlock,
.holdBlock {
  width: var(--next-block);
  height: var(--next-block);
}

.tetrimino-i {
  background: #3498db;
}

.tetrimino-o {
  background: #f1c40f;
}

.tetrimino-t {
  background: #9b59b6;
}

.tetrimino-j {
  background: #1e3799;
}

.tetrimino-l {
  background: #e67e22;
}

.tetrimino-s {
  background: #2ecc71;
}

.tetrimino-z {
  background: #e74c3c;
}

.queue {
  height: var(--next-block) * 5;
  min-width: var(--next-block) * 5;
  background-color: white;
}

.holdQueue {
  /* margin-left: auto; */
}

.gameInformation {
  margin-top: 20px;
}

.box {
  margin-bottom: 1.5em;
  font-size: 1.4em;
}

.scoreData {
  text-align: right;
  ;
  font-weight: bold;
}

.right {
  margin-left: 10px;
}

.afterQueue {
  margin-top: 20px;
}
</style>
