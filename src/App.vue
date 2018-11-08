<template>
  <div id="app" class="game">
    <div class="game-board">
      <Board v-bind:board="current" v-on:button-click="handleClick"/>
    </div>
    <div class="game-info">
      <div class="status">{{status}}</div>
      <ol>
        <li v-for="(move, i) of history" :key="i">
          <button v-on:click="jumpTo(i)">{{i === 0 ? 'Go to game start' : 'Go to move '+i }}</button>
        </li>
      </ol>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Board from './components/Board.vue';

@Component({
  components: {
    Board,
  },
})
export default class App extends Vue {
  private history: Array<{ board: string[][] }>;
  private xIsNext: boolean = true;
  private stepNumber: number = 0;

  private get current(): string[][] {
    return this.history[this.stepNumber].board;
  }

  private get status(): string {
    const winner = this.calculateWinner(this.current);
    if (winner) {
      return 'Winner: ' + winner;
    } else {
      return 'Next player: ' + (this.xIsNext ? 'X' : 'O');
    }
  }

  constructor() {
    super();
    const board = Array(3).fill(undefined);
    for (let i = 0; i < board.length; i++) {
      board[i] = Array(3).fill(undefined);
    }
    this.history = [{ board }];
  }

  private handleClick(x: number, y: number) {
    this.history = this.history.slice(0, this.stepNumber + 1);
    const board = this.current.slice();
    for (const row in board) {
      if (board.hasOwnProperty(row)) {
        board[row] = board[row].slice();
      }
    }

    if (this.calculateWinner(board) || board[y][x]) {
      return;
    }

    board[y][x] = this.xIsNext ? 'X' : 'O';
    this.history = this.history.slice().concat({ board });
    this.xIsNext = !this.xIsNext;
    this.stepNumber = this.history.length - 1;
  }

  private jumpTo(step: number) {
    this.stepNumber = step;
    this.xIsNext = step % 2 === 0;
  }

  private calculateWinner(board: string[][]): null | string {
    const lines = [
      // Verticals
      [{ x: 0, y: 0 }, { x: 0, y: 1 }, { x: 0, y: 2 }],
      [{ x: 1, y: 0 }, { x: 1, y: 1 }, { x: 1, y: 2 }],
      [{ x: 2, y: 0 }, { x: 2, y: 1 }, { x: 2, y: 2 }],
      // Horizontals
      [{ x: 0, y: 0 }, { x: 1, y: 0 }, { x: 2, y: 0 }],
      [{ x: 0, y: 1 }, { x: 1, y: 1 }, { x: 2, y: 1 }],
      [{ x: 0, y: 2 }, { x: 1, y: 2 }, { x: 2, y: 2 }],
      // Diagonals
      [{ x: 0, y: 0 }, { x: 1, y: 1 }, { x: 2, y: 2 }],
      [{ x: 2, y: 0 }, { x: 1, y: 1 }, { x: 0, y: 2 }],
    ];
    for (const line of lines) {
      const [a, b, c] = line;
      if (
        board[a.y][a.x] &&
        board[a.y][a.x] === board[b.y][b.x] &&
        board[a.y][a.x] === board[c.y][c.x]
      ) {
        return board[a.y][a.x];
      }
    }
    return null;
  }
}
</script>

<style lang="scss">
body {
  font: 14px 'Century Gothic', Futura, sans-serif;
  margin: 20px;
}

ol,
ul {
  padding-left: 30px;
}

.status {
  margin-bottom: 10px;
}

.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
</style>
