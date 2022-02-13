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
        <!-- <li><img :src="value.src"
               alt=""></li> -->
        <li v-for="value in src"
            :key="value">
          <img :src="require('../assets/' + value.src + '')"
               @dragstart.prevent>
        </li>
        <li><img :src="require('../assets/' + src[0].src + '')"
               @dragstart.prevent></li>
      </ul>
      <p>
        <span class="pageChange last"
              @click="last">&lt;</span>
        <span class="pageChange next"
              @click="next">&gt;</span>
      </p>
      <ol>
        <li v-for="(value, index) in src"
            :key="value">
          <b :class="{'active' : currentIndex === index || ((currentIndex === src.length)&&(index === 0) ? true:false)}"
             @click.stop="switchItem(index)"></b>
        </li>
      </ol>
    </div>
  </div>
</template>

<script>
export default {
  name: 'RotationPicture',
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
    msg: String,
    src: []
  },
  methods: {
    last () {
      // console.log('上一个')
      this.destroyTimer()
      // const oItems = document.querySelectorAll('#picture>li')
      // const imgWidth = parseFloat(getComputedStyle(oItems[0]).width)
      // const oUl = document.querySelector('#picture')
      const { oItems, imgWidth, oUl } = this.dom()
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
      const { oItems, imgWidth, oUl } = this.dom()
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
      const { imgWidth, oUl } = this.dom()
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
      // console.log(Date.now())
      this.statrtX = event.changedTouches[0].pageX
      this.destroyTimer()
    },
    handleTouchMove (event) {
      // console.log(event.changedTouches[0].pageX)
    },
    handleTouchEnd (event) {
      // console.log(Date.now())
      const endX = event.changedTouches[0].pageX
      this.end(endX)
    },
    mouseDown (event) {
      this.statrtX = event.pageX
      this.destroyTimer()
    },
    mouseUp (event) {
      const endX = event.pageX
      this.end(endX)
    },
    systemJudgment () {
      if (/iPhone|iPad|iPod|Macintosh/i.test(navigator.userAgent)) return false
      if (/Chrome/i.test(navigator.userAgent)) return (/Android/i.test(navigator.userAgent))
      if (/Silk/i.test(navigator.userAgent)) return false
      if (/Android/i.test(navigator.userAgent)) {
        var s = navigator.userAgent.substr(navigator.userAgent.indexOf('Android') + 8, 3)
        return !(parseFloat(s[0] + s[3]) < 44)
      }
    },
    dom () {
      const oItems = document.querySelectorAll('#picture>li')
      const imgWidth = parseFloat(getComputedStyle(oItems[0]).width)
      const oUl = document.querySelector('#picture')
      return {
        oItems: oItems,
        imgWidth: imgWidth,
        oUl: oUl
      }
    },
    end (endX) {
      const { oItems, imgWidth, oUl } = this.dom()
      if (Math.abs(endX - this.statrtX) > 10) {
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
    // background-color: red;
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
        // background-color: black;
        img {
          width: 100%;
          height: 100%;
        }
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
