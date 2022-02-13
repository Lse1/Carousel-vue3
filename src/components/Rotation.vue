<template>
  <div class="rotation">
    <h1>{{ msg }}</h1>
    <div class="rotation-content"
         @touchstart="judgment && handleTouchStart($event)"
         @touchmove="judgment && handleTouchMove($event)"
         @touchend="judgment && handleTouchEnd($event)"
         @mousedown="!judgment && mouseDown($event)"
         @mouseup="!judgment && mouseUp($event)">
      <ul id="picture">
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
        <li>5</li>
        <li>6</li>
        <li>1</li>
      </ul>
      <p>
        <span class="pageChange last"
              @click="last">&lt;</span>
        <span class="pageChange next"
              @click="next">&gt;</span>
      </p>
      <ol>
        <li><b :class="{'active' : currentIndex === 0 || currentIndex === 6}"
             @click.stop="switchItem(0)"></b></li>
        <li><b :class="{'active' : currentIndex === 1}"
             @click.stop="switchItem(1)"></b></li>
        <li><b :class="{'active' : currentIndex === 2}"
             @click.stop="switchItem(2)"></b></li>
        <li><b :class="{'active' : currentIndex === 3}"
             @click.stop="switchItem(3)"></b></li>
        <li><b :class="{'active' : currentIndex === 4}"
             @click.stop="switchItem(4)"></b></li>
        <li><b :class="{'active' : currentIndex === 5}"
             @click.stop="switchItem(5)"></b></li>
      </ol>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Rotation',
  data () {
    return {
      currentIndex: 0,
      timerId: null,
      switchNum: 0,
      rotationId: null,
      statrtX: 0,
      endX: 0,
      judgment: this.systemJudgment()
    }
  },
  props: {
    msg: String
  },
  methods: {
    last () {
      // console.log('上一个')
      this.destroyTimer()
      const oItems = document.querySelectorAll('#picture>li')
      const imgWidth = parseFloat(getComputedStyle(oItems[0]).width)
      const oUl = document.querySelector('#picture')
      this.currentIndex--
      if (this.currentIndex < 0) {
        this.currentIndex = oItems.length - 1
        // 快速的跳转到最后一张
        oUl.style.marginLeft = -this.currentIndex * imgWidth + 'px'
        this.currentIndex--
      }
      this.easeAnimation(oUl, -this.currentIndex * imgWidth)
      this.createTimer()
    },
    next () {
      // console.log('下一个')
      this.destroyTimer()
      const oItems = document.querySelectorAll('#picture>li')
      const imgWidth = parseFloat(getComputedStyle(oItems[0]).width)
      const oUl = document.querySelector('#picture')
      this.currentIndex++
      if (this.currentIndex > oItems.length - 1) {
        this.currentIndex = 0
        // 快速的跳转到第一张
        oUl.style.marginLeft = -this.currentIndex * imgWidth + 'px'
        this.currentIndex++
      }
      this.easeAnimation(oUl, -this.currentIndex * imgWidth)
      this.createTimer()
    },
    easeAnimation (ele, target) {
      clearInterval(this.timerId)
      this.timerId = setInterval(function () {
        // 1.拿到元素当前的位置
        let begin = parseInt(ele.style.marginLeft) || 0
        // 2.定义变量记录步长
        // 公式: (结束位置 - 开始位置) * 缓动系数(0 ~1)
        const step = (target - begin) * 0.3
        // console.log(step)
        // 3.计算新的位置
        begin += step
        if (Math.abs(Math.floor(step)) <= 1) {
          clearInterval(this.timerId)
          begin = target
        }
        // 4.重新设置元素的位置
        ele.style.marginLeft = begin + 'px'
      }, 100)
    },
    switchItem (num) {
      this.destroyTimer()
      const oItems = document.querySelectorAll('#picture>li')
      const imgWidth = parseFloat(getComputedStyle(oItems[0]).width)
      const oUl = document.querySelector('#picture')
      if (this.currentIndex !== num) {
        this.easeAnimation(oUl, -num * imgWidth)
      } else {
      }
      this.currentIndex = num
      this.createTimer()
    },
    createTimer () {
      this.rotationId = setInterval(this.next, 2000)
    },
    destroyTimer () {
      clearInterval(this.rotationId)
    },
    handleTouchStart (event) {
      // console.log(event.changedTouches[0].clientX)
      console.log(event.changedTouches[0].pageX)
      console.log(Date.now())
      this.statrtX = event.changedTouches[0].pageX
      this.destroyTimer()
    },
    handleTouchMove (event) {
      console.log(event.changedTouches[0].pageX)
    },
    handleTouchEnd (event) {
      console.log(event.changedTouches[0].pageX)
      console.log(Date.now())
      const oItems = document.querySelectorAll('#picture>li')
      const imgWidth = parseFloat(getComputedStyle(oItems[0]).width)
      const oUl = document.querySelector('#picture')
      const endX = event.changedTouches[0].pageX
      if (Math.abs(endX - this.statrtX) > 10) {
        console.log(endX - this.statrtX)
        if ((endX - this.statrtX) > 0) {
          this.currentIndex--
          if (this.currentIndex < 0) {
            this.currentIndex = oItems.length - 1
            oUl.style.marginLeft = -this.currentIndex * imgWidth + 'px'
            this.currentIndex--
          }
        } else {
          this.currentIndex++
          if (this.currentIndex > oItems.length - 1) {
            this.currentIndex = 0
            oUl.style.marginLeft = -this.currentIndex * imgWidth + 'px'
            this.currentIndex++
          }
        }
        this.easeAnimation(oUl, -this.currentIndex * imgWidth)
      }
      this.createTimer()
    },
    mouseDown (event) {
      console.log(event)
      this.statrtX = event.pageX
      this.destroyTimer()
    },
    mouseUp (event) {
      console.log(event)
      const oItems = document.querySelectorAll('#picture>li')
      const imgWidth = parseFloat(getComputedStyle(oItems[0]).width)
      const oUl = document.querySelector('#picture')
      const endX = event.pageX
      if (Math.abs(endX - this.statrtX) > 10) {
        console.log(endX - this.statrtX)
        if ((endX - this.statrtX) > 0) {
          this.currentIndex--
          if (this.currentIndex < 0) {
            this.currentIndex = oItems.length - 1
            oUl.style.marginLeft = -this.currentIndex * imgWidth + 'px'
            this.currentIndex--
          }
        } else {
          this.currentIndex++
          if (this.currentIndex > oItems.length - 1) {
            this.currentIndex = 0
            oUl.style.marginLeft = -this.currentIndex * imgWidth + 'px'
            this.currentIndex++
          }
        }
        this.easeAnimation(oUl, -this.currentIndex * imgWidth)
      }
      this.createTimer()
    },
    systemJudgment () {
      if (/iPhone|iPad|iPod|Macintosh/i.test(navigator.userAgent)) return false
      if (/Chrome/i.test(navigator.userAgent)) return (/Android/i.test(navigator.userAgent))
      if (/Silk/i.test(navigator.userAgent)) return false
      if (/Android/i.test(navigator.userAgent)) {
        var s = navigator.userAgent.substr(navigator.userAgent.indexOf('Android') + 8, 3)
        return !(parseFloat(s[0] + s[3]) < 44)
      }
    }
  },
  mounted () {
    this.createTimer()
  },
  unmounted () {
    this.destroyTimer()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.rotation {
  width: 100%;
  height: 100%;
  .rotation-content {
    width: 400px;
    height: 200px;
    margin: 0 auto;
    background-color: red;
    color: blue;
    left: 50%;
    margin-left: -200px;
    line-height: 200px;
    font-size: 30px;
    overflow: hidden;
    position: relative;
    // vertical-align: bottom;
    ul {
      list-style: none;
      display: flex;
      width: 100%;
      padding: 0;
      margin-left: 0px;
      li {
        width: 100%;
        flex: none;
        background-color: black;
      }
    }
    .pageChange {
      // height: 50px;
      color: blue;
      font-size: 30px;
      position: absolute;
      display: inline-block;
      top: 0px;
    }
    .last {
      left: 0px;
    }
    .next {
      right: 0;
    }
    ol {
      width: 100%;
      height: 20px;
      // background-color: yellow;
      position: absolute;
      left: 50%;
      margin-left: -75px;
      bottom: 10px;
      li {
        float: left;
        width: 20px;
        height: 20px;
        // background-color: purple;
        margin-left: 10px;
        b {
          float: left;
          display: inline-block;
          border-radius: 50%;
          width: 8px;
          height: 8px;
          background-color: #ccc;
          border: 2px solid transparent; /*透明色*/
          transition: all 0.5s;
          &.active {
            background-color: red;
          }
        }
      }
    }
  }
}
</style>
