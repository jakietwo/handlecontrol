<template>
  <div class="hello">
    <div id="map1"></div>

    <div class="wrapper"></div>
    <div class="drap" draggable="true">移动</div>

  </div>
</template>

<script>
  export default {
    name: 'HelloWorld',
    data() {
      return {
        x: 116.73, // 人员初始化的经纬度坐标
        y: 39.04,
        tanx: '', // 计算的触摸角度
        map: '', // 绑定map实例到vue
        center: [116.73, 39.04], // 初始化地图中心点
        circleMarker: '', // 全局调用坐标点
        setInvelId: '',
        detance: 0, // 保存用户拖拉距离
      }
    },
    mounted() {
      this._initmap()
      let vm = this
      let dom = document.getElementsByClassName('drap')[0]
      let originX = dom.offsetLeft + 25
      let originY = dom.offsetTop + 25
      console.log(originX + '==' + originY)
      dom.addEventListener('touchstart', function (ev) {
        dom.style.transition = 'none'
        ev.preventDefault()
        let touches = ev.touches[0]
        if (touches) {
          vm.setInvelId = setInterval(() => {
            vm.handleManMove()
          },5)
        }
      })
      dom.addEventListener('touchmove', function (e) {
        e.preventDefault() // 阻止跟随窗口一起运动
        let posX = e.touches[0].pageX
        let posY = e.touches[0].pageY
        // console.log(posX)
        // console.log(posY)
        // 计算角度
        // console.log(e)
        let sinX = posX - originX
        let sinY = originY - posY
        vm.tanx = (Math.atan2(sinY, sinX) / Math.PI * 180).toPrecision(3)
        // console.log('X相对移动距离',sinX)
        // console.log('Y相对移动距离',sinY)
        // console.log('角度值', vm.tanx)
        vm.detance = Math.pow(posX - originX, 2) + Math.pow(posY - originY, 2)
        if (vm.detance >= 3600) {
          let x1, y1
          x1 = originX + 60 * Math.cos(vm.tanx * 3.14 / 180)
          y1 = originY - 60 * Math.sin(vm.tanx * 3.14 / 180)
          // console.log('圆上的点坐标是:', x1, '==', y1)
          dom.style.left = x1 - 25 + 'px'
          dom.style.top = y1 - 25 + 'px'
        } else {
          dom.style.left = posX - 25 + 'px'
          dom.style.top = posY - 25 + 'px'
        }
        // 设置一个定时器 任务移动
        // if (e.touches.length){
        //   vm.handleManMove(vm.tanx, detance)
        // }
      })
      dom.addEventListener('touchend', function (ev) {
        dom.style.left = originX - 25 + 'px'
        dom.style.top = originY - 25 + 'px'
        dom.style.transition = 'all .2s'
        clearInterval(vm.setInvelId)
      })
    },
    methods: {
      _initmap() {
        this.map = new AMap.Map('map1', {
          zoom: 12,
          center: this.center
        })
        this.circleMarker = new AMap.CircleMarker({
          center: this.center,
          strokeColor: 'white',
          radius: 6,
        })
        this.circleMarker.setMap(this.map)
      },
      // 计算人员移动
      handleManMove() {
        //   center: [116.73, 39.04]
        // console.log('1111', this.detance)
        let speed
        if (this.detance) {
          if(this.detance <=4800 ){
            speed = (this.detance / 1000000000).toPrecision(3)
          }else {
            speed = (4800/ 1000000000).toPrecision(3)
          }
        } else {
          speed = 0
        }

        // console.log('速度',speed)
        // 计算在速度下的X,Y 坐标 移动速度
        let speedX = speed * (Math.cos(Math.PI / 180  * this.tanx).toPrecision(2))
        let speedY = speed * (Math.sin(Math.PI / 180  * this.tanx).toPrecision(2))
        // console.log('速度x',speedX)
        // console.log('速度y',speedY)
        this.x += speedX
        this.y += speedY
        let position = new AMap.LngLat(this.x, this.y)
        this.circleMarker.setCenter(position)
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #map1 {
    width: 100vw;
    height: 100vh;
  }

  .wrapper {
    position: fixed;
    bottom: 65px;
    left: 50px;
    width: 120px;
    height: 120px;
    z-index: 10;
    border-radius: 50%;
    background-color: gray;
    opacity: .2;
    box-shadow: 0 0 4px rgba(7, 17, 27, .1);
  }

  .drap {
    position: absolute;
    z-index: 20;
    bottom: 85px;
    left: 85px;
    line-height: 50px;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    background-color: orange;
    box-sizing: content-box;
  }
</style>
