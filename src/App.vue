<template>
  <div id='carouselMap'>
    <img
      v-for="(image, index) in nameList"
      :key="index"
      :src="basicPath + image"
      :alt="index"
    />
    <nav @mousemove="myanimation($event)">
      <img
        v-for="(image, index) in nameList"
        :key="index"
        :src="basicPath + image"
        :alt="index"
      />
    </nav>
  </div>
</template>

<script>
export default {
  name: 'App',
  data: function () {
    return {
      basicPath: 'http://localhost:1010/src/images/',
      nameList: [],
      imageList: [],
      totalNum: 0,
      topNum: 0
    }
  },
  updated: function () {
    this.imageList = document.querySelectorAll('#carouselMap>img')
    this.doSelction()
    this.autoChange()
  },
  mounted: function () {
    var requestImages = new XMLHttpRequest()
    requestImages.open('get', 'http://localhost:1000/getImageList', true)
    var that = this
    requestImages.onreadystatechange = function () {
      if (requestImages.readyState == 4) {//eslint-disable-line
        if (requestImages.status == 200 || requestImages.status == 0) {//eslint-disable-line
          that.nameList = JSON.parse(requestImages.responseText)['nameList']
          that.totalNum = that.nameList.length
        }
      }
    }
    requestImages.send(null)
  },
  methods: {
    myanimation: function (event) {
      var target = event.target
      if (target.nodeName.toLowerCase() == 'img') {//eslint-disable-line
        this.topNum = Number(target.alt)
      }
      this.doSelction()
    },
    doSelction: function () {
      for (let item of this.imageList) {
        if (item.alt == this.topNum) {//eslint-disable-line
          item.style.opacity = '1'
        } else {
          item.style.opacity = '0'
        }
      }
    },
    autoChange () {
      var that = this
      setInterval(() => {
        that.topNum = (that.topNum + 1) % that.totalNum
        that.doSelction()
      }, 3000)
    }
  }
}
</script>

<style>
body:after {
  content: "";
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  opacity: 0.3;
  z-index: -1;
  background-image: url("./assets/logo.png");
  background-repeat: no-repeat;
  background-size: 100% 100%;
  position: absolute;
}
#carouselMap {
  width: 1000px;
  height: 600px;
  overflow: hidden;
  margin: 30px auto;
  position: relative;
}
#carouselMap > img {
  position: absolute;
  top: 10px;
  left: 100px;
  width: 700px;
  height: 500px;
  opacity: 0;
  display: block;
  border-radius: 10px;
  box-shadow: 3px 3px 3px rgb(125, 107, 139);
  transition: all 2s;
}
nav {
  position: absolute;
  height: 80px;
  width: 700px;
  left: 100px;
  bottom: 0px;
  display: flex;
  justify-content: space-between;
  overflow: hidden;
}
nav > img {
  border-radius: 5px;
  width: 60px;
  flex: 1;
  opacity: 0.7;
}
nav > img:hover {
  opacity: 1;
  transition: all 1s;
  transform: scale(1.1);
}
</style>
