<template lang="pug">
  .imager
    canvas#canvas.canvas
    .upload
      input(type="file" name="file" id="file" @change="onFileChange")
    .name
      input(type="text" name="name" id="mame" v-model="name" @change="onMsgChange")
    .message
      // input(type="text" name="message" id="message" v-model="message" @change="onMsgChange")
      textarea(name="example" cols="50" rows="10" v-model="message" @change="onMsgChange")
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
    name: {
      type: String,
      default: 'てきすと'
    },
    message: {
      type: String,
      default: 'ここに文章が入ります。ここに文章が入ります。ここに文章が入ります。ここに文章が入ります。ここに文章が入ります。ここに文章が入ります。'
    },
    messageBoxSrc: {
      type: String,
      default: '/static/fukidashi02.png'
    }
  },
  // watch: {
  //   src () {
  //     this.loadLocalImage()
  //     this.draw()
  //   }
  // },
  mounted () {
    this.file = document.getElementById('file')
    this.result = document.getElementById('result')

    this.canvas = this.$el.querySelector('#canvas')
    this.ctx = this.canvas.getContext('2d')

    // canvas上に画像を表示
    this.img = new Image()
    this.messageBoxImage = new Image()
    this.messageBoxImage.src = this.messageBoxSrc
    this.img.src = 'https://www.tam-tam.co.jp/tipsnote/wpdata/wp-content/uploads/2017/10/canvas_image.jpg'
    this.canvasDraw()
  },
  methods: {
    onFileChange (e) {
      this.fileList = e.target.files || e.dataTransfer.files
      this.loadLocalImage(this.fileList[0])
    },
    onMsgChange () {
      this.canvasDraw()
    },
    loadLocalImage (file) {
      this.fileData = file

      if (!this.fileData.type.match('image.*')) {
        alert('画像を選択してください')
        return
      }
      this.reader = new FileReader()
      this.reader.onloadend = () => {
        this.img = new Image()
        this.img.onload = function () {
          console.log(this.img)
        }
        this.img.src = this.reader.result
        console.log(this.img.naturalWidth)

        // console.log(this.img.naturalHeight)
        // this.result.appendChild(this.img)
        this.canvasDraw()
      }
      // 読み込みの実行
      this.reader.readAsDataURL(this.fileData)
    },
    canvasDraw () {
      // console.log(this.img.naturalHeight) // => だめ
      this.ctx.clearRect(0, 0, 300, 150)
      let image = new Image()
      image.onload = () => {
        this.ctx.drawImage(image, 0, 0, 300, 150)
        this.ctx.drawImage(this.messageBoxImage, 0, 0, 300, 150)
        this.addText()

        // console.log(this.canvas) // => ok
        // let data = this.canvas.toDataURL('image/png')
        // let outputImg = document.createElement('img')
        // outputImg.src = data
        // console.log(data)
        // this.result.appendChild(outputImg)
      }
      image.src = this.img.src
    },
    addText () {
      let nameFontSize = '20px'
      let messageFontSize = '20px'

      this.ctx.textAlign = 'left'
      this.ctx.textBaseline = 'middle'

      this.ctx.fillStyle = '#FFFFFF'
      this.ctx.font = `normal ${nameFontSize} 'Noto Sans JP'`
      this.ctx.fillText(this.name, 20, 25)

      this.ctx.fillStyle = '#002B69'
      this.ctx.font = `normal ${messageFontSize} 'Noto Sans JP'`
      this.ctx.fillText(this.message, 80, 100)
    }
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
