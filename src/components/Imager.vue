<template lang="pug">
  .imager
    .info
      h1 FF14 セリフジェネレータ
    h2 設定するところ
    .setting
      .upload
        p
          span.inputName 画像：
          input.inputArea.-image(type="file" name="file" id="file" @change="onFileChange")
      hr
      .name
        p
          span.inputName キャラクター名：
          input.inputArea.-text(type="text" name="name" id="name" v-model="name" @change="canvasDraw")
      hr
      .message
        p
          span.inputName セリフ：
          textarea.inputArea.-text.-serif(name="message" v-model="message" @change="canvasDraw")
        p
          span.inputName 巨大化：
          input.inputArea(type="checkbox" name="cursol" v-model="messageBigFlg" @change="canvasDraw")
      hr
      .cursol
        p
          span.inputName カーソル：
          input.inputArea(type="checkbox" name="cursol" v-model="messageBoxSrc" true-value="static/fukidashi02.png" false-value="static/fukidashi01.png" @change="onCursolChange")
      hr
      .copyright
        p
          span.inputName コピーライト：
          textarea.inputArea.-text(name="copyright" v-model="copyright" @change="canvasDraw")
        p
          span.inputName ON/OFF：
          input.inputArea(type="checkbox" name="cursol" v-model="copyrightFlg" @change="onCursolChange")
    .no-display
      img(src="static/fukidashi01.png")
      img(src="static/fukidashi02.png")
    #result
    h2 できあがり
    canvas#canvas.canvas

    .download
      a.button#downloadButton(@click="download")
        |ダウンロードボタン

    .footer
      hr
      p IEは動きません。ボタンを押しても直接DLされない場合は、表示された画像を右クリック/長押しなどをしてみてください。
      p
        |フキダシ素材は
        a(href="http://xiv.umee.info/material-01/" target="_blank") こちら
        |よりお借りいたしました。
      p
        |記載されている会社名・製品名・システム名などは、各社の商標、または登録商標です。
        br
        |Copyright (C) 2010 - 2019 SQUARE ENIX CO., LTD. All Rights Reserved.
      p
        |ご意見ご要望は
        a(href="https://twitter.com/kid_nanarya/")
          |@kid_nanarya
        |まで
      p
        |ハッシュタグはこちら
        a(href="https://twitter.com/search?q=%23FF14%E3%82%BB%E3%83%AA%E3%83%95%E3%82%B8%E3%82%A7%E3%83%8D%E3%83%AC%E3%83%BC%E3%82%BF" target="_blank")
          |#FF14セリフジェネレータ
      p
        a.twitter-share-button(href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-via="kid_nanarya" data-hashtags="FF14セリフジェネレータ" data-show-count="false") SHARE

</template>

<script>
export default {
  name: 'Imager',
  data () {
    return {
      messageBoxSrc: 'static/fukidashi02.png',
      messageBigFlg: false,
      copyrightFlg: false
    }
  },
  props: {
    name: {
      type: String,
      default: 'りゅーさん'
    },
    message: {
      type: String,
      default: 'リミットブレイク！！！'
    },
    copyright: {
      type: String,
      default: 'Copyright (C) 2010 - 2019 SQUARE ENIX CO., LTD. All Rights Reserved.'
    }
  },
  mounted () {
    this.file = document.getElementById('file')
    this.result = document.getElementById('result')
    this.canvas = this.$el.querySelector('#canvas')
    this.ctx = this.canvas.getContext('2d')

    // canvas上に画像を表示
    this.img = new Image()
    this.img.src = 'static/fantasy_ryuukishi.png'
    this.messageBoxImage = new Image()
    this.messageBoxImage.src = this.messageBoxSrc
    this.canvasDraw()
  },
  methods: {
    onFileChange (e) {
      this.fileList = e.target.files || e.dataTransfer.files
      this.loadLocalImage(this.fileList[0])
    },

    onCursolChange () {
      this.messageBoxImage.src = this.messageBoxSrc
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
        this.addCopyright()
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
        w: this.imageHeight * (180 / 900) * (657 / 158), // 画像サイズ
        h: this.imageHeight * (180 / 900)
      }

      if (this.messageBoxPosition.w > this.imageWidth) { // 縦長画像対応
        this.messageBoxPosition.x = this.imageWidth * 0.05
        this.messageBoxPosition.w = this.imageWidth * 0.9
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
      if (this.messageBigFlg) {
        messageFontSize = this.messagePosition.size * 3 + 'px'
        this.messagePosition.y = this.imageHeight * (805 / 900)
      }

      this.ctx.textAlign = 'left'
      this.ctx.textBaseline = 'middle'

      this.ctx.fillStyle = '#FFFFFF'
      this.ctx.font = `normal ${nameFontSize} 'Noto Sans JP'`
      this.ctx.fillText(this.name, this.namePosition.x, this.namePosition.y)

      this.ctx.fillStyle = '#000000'
      this.ctx.font = `500 ${messageFontSize} 'Noto Sans JP'`

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
    },

    addCopyright () {
      if (this.copyrightFlg) {
        let copyFontSize = this.imageHeight * (10 / 900) + 'px'

        this.ctx.textAlign = 'left'
        this.ctx.textBaseline = 'middle'

        this.ctx.fillStyle = '#000000'
        this.ctx.font = `normal ${copyFontSize} 'Noto Sans JP'`
        this.ctx.fillText(this.copyright, 11, this.imageHeight * 0.99 + 1)

        this.ctx.fillStyle = '#FFFFFF'
        this.ctx.fillText(this.copyright, 10, this.imageHeight * 0.99)
      }
    },

    download () {
      // secret special thanks: https://croagunk.github.io/FFXIV-Boss-Subtitle-Generator/
      let canvas = document.getElementById('canvas')
      let base64 = canvas.toDataURL()
      let blob = this.base64toBlob(base64)
      let a = document.getElementById('downloadButton')
      let filename = `xivspeech_${(new Date()).getTime()}.png`

      a.href = window.URL.createObjectURL(blob)
      a.download = filename
      // a.click()
    },
    base64toBlob (base64) {
      let tmp = base64.split(',')
      let data = atob(tmp[1])
      let mime = tmp[0].split(':')[1].split(';')[0]
      let buf = new Uint8Array(data.length)
      for (let i = 0; i < data.length; i++) {
        buf[i] = data.charCodeAt(i)
      }
      let blob = new Blob([buf], { type: mime })
      return blob
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="stylus">
h1
  @media screen and (max-width: 767px)
    font-size 7vw
h2
  @media screen and (max-width: 767px)
    font-size 6vw

.canvas
  max-width 100%
.download
  padding 20px 0
.button
  background-color: #E11
  padding 10px
  color #fff
  font-weight bold
  cursor pointer
  text-decoration none
  &:hover
    opacity 0.7

.setting
  border 1px solid #ccc
  padding: 20px

.inputName
  @media screen and (min-width: 768px)
    // text-align right
    display inline-block
    width 10em
  @media screen and (max-width: 767px)
    display block

.inputArea
  display inline-block
  font-size 1rem
  &.-text
    width 100%
    max-width 66em
    border 1px solid #ccc
  &.-serif
    min-height 4em
.no-display
  display none

.footer
  font-size 0.8rem
</style>
