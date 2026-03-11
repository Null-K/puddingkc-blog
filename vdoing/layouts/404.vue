<template>
  <div class="theme-container error-page">
    <div class="error-content">
      <img src="/img/404.webp" alt="404" class="error-image" />
      <p class="error-text">{{ getMsg() }}</p>
      <router-link to="/" class="back-button">返回首页</router-link>
    </div>
  </div>
</template>

<script>
const msgs = [
  `这里什么都没有`,
  `页面走丢了`,
  `找不到这个页面`,
  `链接好像坏掉了`
]

export default {
  methods: {
    getMsg () {
      return msgs[Math.floor(Math.random() * msgs.length)]
    }
  }
}
</script>

<style lang="stylus" scoped>
.theme-container.error-page
  min-height 100vh
  display flex
  align-items center
  justify-content center
  background var(--bodyBg)
  
.error-content
  text-align center
  padding 2rem
  
.error-image
  max-width 400px
  width 100%
  height auto
  margin-bottom 2rem
  animation float 3s ease-in-out infinite
  
.error-text
  font-size 1.2rem
  color var(--textColor)
  opacity 0.8
  margin-bottom 2rem
  
.back-button
  display inline-block
  padding 0.8rem 2rem
  background-color $accentColor
  color #fff
  border-radius 4px
  text-decoration none
  transition all 0.3s
  font-size 1rem
  
  &:hover
    background-color lighten($accentColor, 10%)
    transform translateY(-2px)
    box-shadow 0 4px 12px rgba(0,0,0,0.15)

@keyframes float
  0%, 100%
    transform translateY(0)
  50%
    transform translateY(-10px)

@media (max-width 719px)
  .error-image
    max-width 300px
  .error-text
    font-size 1rem
  .back-button
    padding 0.6rem 1.5rem
    font-size 0.9rem
</style>
