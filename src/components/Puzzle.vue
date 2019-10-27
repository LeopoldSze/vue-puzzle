<template>
  <div class="puzzle" :style="{ width: width + 'px', height: height + 'px' }">
    <div
      v-for="(item, index) in blockPoints"
      :key="item.id"
      :style="{
        width: blockWidth + 'px',
        height: blockHeight  + 'px',
        left: `${item.x}px`,
        top: `${item.y}px`,
        backgroundImage: `url(${img})`,
        backgroundPosition: `-${correctPoints[index].x}px -${correctPoints[index].y}px`,
        opacity: index === blockPoints.length -1 ? 0.2 : 1
      }"
      @click="handleClick"
      :ref="index === blockPoints.length -1 ? 'empty' : 'block'"
      :data-correctX="correctPoints[index].x"
      :data-correctY="correctPoints[index].y"
      class="puzzle__block"
    >
    </div>
    <img :src="img" alt="">
    <div class="control">
      <button type="button" @click="reload">重置</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Puzzle',
  props: {
    width: {
      type: Number,
      default: 500
    },
    height: {
      type: Number,
      default: 500
    },
    row: {
      type: Number,
      default: 3
    },
    col: {
      type: Number,
      default: 3
    },
    img: {
      type: String,
      required: true,
      default: ''
    }
  },
  computed: {
    blockWidth () {
      return this.width / this.col
    },
    blockHeight () {
      return this.height / this.row
    },
    correctPoints () {
      const { row, col } = this
      const arr = []
      for (let i = 0; i < row; i++) {
        for (let j = 0; j < col; j++) {
          arr.push({
            id: new Date().getTime() + Math.random() * 100,
            x: j * this.blockWidth,
            y: i * this.blockHeight
          })
        }
      }
      return arr
    },
    blockPoints () {
      const points = this.correctPoints
      const len = points.length
      const lastFile = points[len - 1]
      const newArr = [...points]
      newArr.pop()
      newArr.sort(() => Math.random() - 0.5)
      newArr.push(lastFile)
      return newArr
    }
  },
  methods: {
    handleClick (e) {
      const blockObj = e.target
      const emptyObj = this.$refs.empty[0]
      if (!this.isAdjacent(blockObj, emptyObj)) {
        return
      }
      const { left, top } = blockObj.style
      blockObj.style.left = emptyObj.style.left
      blockObj.style.top = emptyObj.style.top
      emptyObj.style.left = left
      emptyObj.style.top = top
      const flag = this.checkCorrect()
      if (flag) {
        this.winGame(emptyObj)
      }
    },
    isAdjacent (blockObj, emptyObj) {
      const { left: blockLeft, top: blockTop, width, height } = blockObj.style
      const { left: emptyLeft, top: emptyTop } = emptyObj.style
      const xDis = Math.floor(Math.abs(parseFloat(blockLeft) - parseFloat(emptyLeft)))
      const yDis = Math.floor(Math.abs(parseFloat(blockTop) - parseFloat(emptyTop)))
      const flag = (blockLeft === emptyLeft && yDis === parseInt(height)) || (blockTop === emptyTop && xDis === parseInt(width))
      return flag
    },
    checkCorrect () {
      const blockObj = this.$refs.block
      return blockObj.every(item => {
        const { left: itemLeft, top: itemTop } = item.style
        const { correctx: correctX, correcty: correctY } = item.dataset
        const flag = (parseInt(itemLeft) === parseInt(correctX) && parseInt(itemTop) === parseInt(correctY))
        return flag
      })
    },
    winGame (empty) {
      setTimeout(() => {
        empty.style.opacity = 1
        setTimeout(() => {
          alert('恭喜， 拼图完成啦！')
          this.goToNextLevel()
        }, 300)
      }, 300)
    },
    goToNextLevel () {
      const answerFlag = window.confirm('是否进入下一关？')
      if (answerFlag) {
        this.$emit('next')
      }
    },
    reload () {
      location.reload()
    }
  }
}
</script>

<style scoped lang="less">
  .puzzle{
    border: 2px solid #ccc;
    margin: 50px auto;
    position: relative;
    .puzzle__block{
      background: #00ff00;
      border: 1px solid #aaa;
      box-sizing: border-box;
      cursor: pointer;
      position: absolute;
      transition: all .3s;
    }
    img{
      width: 150px;
      height: 150px;
      position: absolute;
      top: 150px;
      right: -250px;
      border: 1px solid #007aff;
    }
    .control{
      position: absolute;
      top: 550px;
      left: 40%;
      button{
        width: 120px;
        height: 50px;
        border-radius: 10px;
        border: none;
        cursor: pointer;
        font-size: 16px;
        color: #fff;
        background: #93BC4D;
        &:hover{
          background: #33C9FF;
        }
        &:first-of-type{
          margin-right: 50px;
        }
      }
    }
  }
</style>
