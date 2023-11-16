<script setup>
import { shallowRef, computed, reactive, onMounted } from "vue";
import ThemeSwitcher from "./ThemeSwitcher.vue";

const progress = shallowRef(true);
const players = {
  0: "X",
  1: "O",
};
const player = shallowRef(0);
const isDraw = shallowRef(false);
const playerName = computed(() => players[player.value]);
let board = reactive(Array(3).fill(0).map(() => Array(3).fill('')));

const switchPlayer = () => {
  player.value = player.value === 0 ? 1 : 0;
};

const newGame = () => {
  for (const row in board) {
    board[row] = Array(3).fill('');
  }

  isDraw.value = false;
  progress.value = true;
}

const move = (row, col) => {
  if (!progress.value || board[row][col]) {
    return this;
  }

  board[row][col] = playerName.value;

  if (!sameRows() && !sameCols() && !sameDiagonals()) {
    if (checkDraw()) {
      isDraw.value = true;
    } else {
      return switchPlayer();
    }
  }

  progress.value = false;
};

const sameRows = () => {
  for (let index = 0; index < board.length; index++) {
    if (board[index][0] && board[index].every((val, index, arr) => val === arr[0])) {
      return true;
    }
  }

  return false;
};

const sameCols = () => {
  for (let index = 0; index < board.length; index++) {
    if (board[0][index] && board.every((val, _, arr) => val[index] === arr[0][index])) {
      return true;
    }
  }

  return false;
};

const sameDiagonals = () => {
  return !!(board[0][0] && board.every((val, index, arr) => val[index] === arr[0][0]) ||
            board[2][0] && board.every((val, index, arr) => val[val.length - 1 - index] === arr[2][0]));
};

const checkDraw = () => board.every(row => row.every(col => col));
</script>
<template>
  <ThemeSwitcher />
  <h1 class="my-8 text-3xl font-bold text-center dark:text-white">Tic-Tac-Toe</h1>
  <div class="my-8 text-center dark:text-white">
    <h2 v-if="progress"
        class="text-2xl"
    >
      Player {{ playerName }}'s turn
    </h2>
    <h2 v-else
        class="text-4xl font-bold"
    >
      <template v-if="isDraw">
        Draw!
      </template>
      <template v-else>
        Player '{{ playerName }}' wins!
      </template>
    </h2>
    <button v-if="!progress"
            @click="newGame"
            class="px-4 py-2 mt-4 text-m font-bold text-white bg-blue-500 rounded-md uppercase tracking-wide hover:bg-blue-700 transition-colors duration-300 ease-in-out"
    >
      New game
    </button>
  </div>
  <div class="mt-12 flex justify-center">
    <div class="grid grid-flow-row grid-rows-3 grid-cols-3">
      <template v-for="(row, rowIndex) in board"
                :key="rowIndex"
      >
        <template v-for="(col, colIndex) in row"
                  :key="colIndex"
        >
          <button @click="move(rowIndex, colIndex)"
                  class="flex items-center justify-center w-32 h-32 border border-gray-400 text-5xl"
                  :class="{
                    'text-emerald-500': col === players[0],
                    'text-orange-500': col === players[1],
                  }"
          >
            {{ col }}
          </button>
        </template>
      </template>
    </div>
  </div>
</template>
