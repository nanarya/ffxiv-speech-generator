<template lang="pug">
  .imager
    canvas#canvas.canvas
    .upload
      p
        span.inputName 画像：
        input.inputArea(type="file" name="file" id="file" @change="onFileChange")
    hr
    .name
      p
        span.inputName キャラクター名：
        input.inputArea(type="text" name="name" id="mame" v-model="name" @change="onMsgChange")
    hr
    .message
      p
        span.inputName セリフ：
        textarea.inputArea(name="example" cols="66" rows="4" v-model="message" @change="onMsgChange")
    hr
    #result
    h1 セリフジェネレータ（仮）
    p このページはβ版です。いろいろきちんとできたら移設します。
      br
    h2 使い方
      br
    ol
      li 画像を選択してキャラクター名とセリフを入れてください
      li 右クリックで画像保存できます。
    h2 現在確認されている不具合
    ul
      li 縦長画像に対応していません
    hr
    p 記載されている会社名・製品名・システム名などは、各社の商標、または登録商標です。
    p Copyright (C) 2010 - 2019 SQUARE ENIX CO., LTD. All Rights Reserved.

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
      default: 'おひげ'
    },
    message: {
      type: String,
      default: 'がおー！'
    },
    messageBoxSrc: {
      type: String,
      default: 'static/fukidashi02.png' // ここどうしよう？
    }
  },
  mounted () {
    this.file = document.getElementById('file')
    this.result = document.getElementById('result')
    this.canvas = this.$el.querySelector('#canvas')
    this.ctx = this.canvas.getContext('2d')

    // canvas上に画像を表示
    this.img = new Image()
    this.img.src = 'static/default.jpg' // ここどうしよう？
    this.messageBoxImage = new Image()
    this.messageBoxImage.src = this.messageBoxSrc
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
    loadLocalImage (fileData) {
      if (!fileData.type.match('image.*')) {
        alert('画像を選択してください')
        return
      }
      this.reader = new FileReader()
      this.img = new Image()
      this.reader.onloadend = () => {
        this.img.onload = () => {
          this.canvasDraw()
        }
        this.img.src = this.reader.result
      }
      // 読み込みの実行
      this.reader.readAsDataURL(fileData)
    },
    canvasDraw () {
      let image = new Image()
      image.onload = () => {
        this.getSize(image)

        // canvas の初期化
        this.canvas.width = this.imageWidth
        this.canvas.height = this.imageHeight
        this.ctx.clearRect(0, 0, this.imageWidth, this.imageHeight)

        this.ctx.drawImage(image, 0, 0, this.imageWidth, this.imageHeight)
        this.ctx.drawImage(
          this.messageBoxImage,
          this.messageBoxPosition.x,
          this.messageBoxPosition.y,
          this.messageBoxPosition.w,
          this.messageBoxPosition.h
        )
        this.addText()
      }
      image.src = this.img.src
    },
    getSize (image) {
      // let width = image.naturalWidth
      // let height = image.naturalHeight

      let ratio = image.naturalWidth / image.naturalHeight
      this.imageWidth = image.naturalWidth
      this.imageHeight = this.imageWidth / ratio

      this.messageBoxPosition = {
        x: (this.imageWidth / 2) - (this.imageHeight * (180 / 900) * (657 / 158) / 2),
        y: this.imageHeight * (715 / 900),
        w: this.imageHeight * (180 / 900) * (657 / 158),
        h: this.imageHeight * (180 / 900)
      }

      this.namePosition = {
        x: this.messageBoxPosition.x + (this.messageBoxPosition.w * 0.08),
        y: this.imageHeight * (737 / 900),
        size: Math.round(this.imageHeight * 0.021)
      }

      this.messagePosition = {
        x: this.namePosition.x,
        y: this.imageHeight * (777 / 900),
        size: this.namePosition.size
      }

      // console.log('=====================')
      // console.log(this.namePosition.size)
      // console.log('=====================')

      // console.log(ratio)
      // console.log(this.imageWidth)
      // console.log(this.imageHeight)

      // console.log('this.messageBoxPosition')
      // console.log(this.messageBoxPosition)
      // console.log('this.namePosition')
      // console.log(this.namePosition)
      // console.log('this.messagePosition')
      // console.log(this.messagePosition)
    },
    addText () {
      let nameFontSize = this.namePosition.size + 'px'
      let messageFontSize = this.messagePosition.size + 'px'

      this.ctx.textAlign = 'left'
      this.ctx.textBaseline = 'middle'

      this.ctx.fillStyle = '#FFFFFF'
      this.ctx.font = `normal ${nameFontSize} 'Noto Sans JP'`
      this.ctx.fillText(this.name, this.namePosition.x, this.namePosition.y)

      this.ctx.fillStyle = '#000000'
      this.ctx.font = `normal ${messageFontSize} 'Noto Sans JP'`

      const LINE_HEIGHT = 1.4
      for (var lines = this.message.split('\n'), i = 0, l = lines.length; l > i; i++) {
        let line = lines[i]
        let addY = 0

        // 2行目以降の水平位置は行数とlineHeightを考慮する
        if (i) addY += this.namePosition.size * LINE_HEIGHT * i
        this.ctx.fillText(line, this.messagePosition.x, this.messagePosition.y + addY)
        // context.fillText( line, x + 0, y + addY )
      }
      // this.ctx.fillText(this.message, this.messagePosition.x, this.messagePosition.y)
    }
  }
}
// https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="stylus">
a
  color #42b983
.canvas
  width 100%
.inputName
  display inline-block
  width 10em
.inputArea
  display inline-block
</style>
