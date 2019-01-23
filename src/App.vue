<template lang="pug">
  #app
    img(src="./assets/logo.png")
    Imager
</template>

<script>
import Imager from './components/Imager'

export default {
  name: 'App',
  components: {
    Imager
  }
}
</script>

<style lang="stylus">
@import url(http://fonts.googleapis.com/earlyaccess/notosansjp.css)
#app
  font-family 'Noto Sans JP', 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing antialiased
  -moz-osx-font-smoothing grayscale
  // text-align center
  color #2c3e50
  margin-top 60px
</style>
