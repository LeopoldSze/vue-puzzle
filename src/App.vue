<template>
  <div id="app">
    <Puzzle v-bind="puzzle[level]" @next="handleNext"></Puzzle>
  </div>
</template>

<script>
import Puzzle from './components/Puzzle.vue'

export default {
  name: 'app',
  components: {
    Puzzle
  },
  data () {
    return {
      level: 0,
      puzzle: [
        { row: 3, col: 3, img: '../1.png' },
        { row: 4, col: 4, img: '../1.png' },
        { row: 5, col: 5, img: '../1.png' },
        { row: 6, col: 6, img: '../1.png' },
        { row: 7, col: 7, img: '../1.png' }
      ]
    }
  },
  methods: {
    handleNext () {
      if (++this.level === this.puzzle.length) {
        const flag = window.confirm('已经是最后一关了，是否重新进入第一关？')
        if (flag) {
          this.level = 0
        }
      }
    }
  }
}
</script>

<style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
