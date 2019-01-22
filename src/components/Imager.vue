<template lang="pug">
  .imager
    canvas#canvas.canvas(width="400" height="300")
    .upload
      input(type="file" name="file" id="file" @change="onFileChange")
    .message
      input(type="text" name="text" id="message" v-model="message" @change="onMsgChange")
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
    message: {
      type: String,
      default: 'hogehoge'
    },
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
      // console.log('onFileChange') //=> ok
      this.fileList = e.target.files || e.dataTransfer.files
      this.loadLocalImage(this.fileList[0])
    },
    onMsgChange () {
      this.canvasDraw()
    },
    loadLocalImage (file) {
      // console.log(file) // => ok
      this.fileData = file

      if (!this.fileData.type.match('image.*')) {
        alert('画像を選択してください')
        return
      }
      this.reader = new FileReader()
      console.log('this.reader')
      console.log(this.reader)
      this.reader.onload = () => {
        // let img = document.createElement('img')
        this.img.src = this.reader.result
        this.result.appendChild(this.img)
        this.canvasDraw()
      }
      this.reader.readAsDataURL(this.fileData)
    },
    canvasDraw () {
      this.ctx.clearRect(0, 0, 400, 300)
      let image = new Image()
      image.src = this.img.src
      image.onload = () => {
        this.ctx.drawImage(image, 0, 0, 400, 300)
        this.addText()
      }
    },
    addText () {
      this.ctx.fillStyle = '#fdd000'
      this.ctx.fillRect(10, 10, 140, 30)

      this.ctx.font = "bold 20px 'Noto Sans JP'"
      this.ctx.textAlign = 'left'
      this.ctx.textBaseline = 'middle'
      this.ctx.fillStyle = '#002B69'
      this.ctx.fillText(this.message, 80, 25)
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
