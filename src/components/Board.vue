<template>
  <div>
    <div class="board-row" v-for="(row, y) of board" :key="y">
      <Square
        v-for="(square, x) of row"
        :key="x"
        v-bind:value="square"
        v-on:on-click="handleClick(x, y)"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import Square from './Square.vue';

@Component({ components: { Square } })
export default class Board extends Vue {
  @Prop()
  private board!: string[][];
  private handleClick(x: number, y: number) {
    this.$emit('button-click', x, y);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.board-row:after {
  clear: both;
  content: '';
  display: table;
}
</style>
