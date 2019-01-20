<template lang="pug">
  .imager
    canvas#canvas.canvas(width="400" height="300")
    .upload
      input(type="file" name="file" id="file" @change="onFileChange")
    #result
    p aaaaaaaaaaaa
</template>

<script>
export default {
  name: 'Imager',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  },
  props: {
    src: {
      type: String,
      default: 'https://www.tam-tam.co.jp/tipsnote/wpdata/wp-content/uploads/2017/10/canvas_image.jpg'
    }
  },
  watch: {
    src () {
      this.loadLocalImage()
      this.draw()
    }
  },
  methods: {
    log () {
      // console.log(canvas)
      // console.log(canvasHeight)
    },
    draw () {
      this.ctx.beginPath()
      this.ctx.clearRect(0, 0, 400, 300)
      this.img.src = this.src
      this.ctx.drawImage(this.img, 0, 0, 400, 300)
    },
    onFileChange (e) {
      console.log('onFileChange')
      this.fileList = e.target.files || e.dataTransfer.files
      this.loadLocalImage(this.fileList[0])
    },
    loadLocalImage (file) {
      this.fileData = file

      if (!this.fileData.type.match('image.*')) {
        alert('画像を選択してください')
        return
      }
      this.reader = new FileReader()
      let img = document.createElement('img')
      img.src = this.reader.result
      this.result.appendChild(this.img)
      this.reader.readAsDataURL(this.fileData)
    }
  },
  mounted () {
    this.file = document.getElementById('file')
    this.result = document.getElementById('result')

    this.ctx = this.$el.querySelector('#canvas').getContext('2d')

    // canvas上に画像を表示
    this.img = new Image()
    this.draw()
  }
}
// https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="stylus">
h1, h2
  font-weight normal
ul
  list-style-type none
  padding 0
li
  display inline-block
  margin 0 10px
a
  color #42b983
</style>
