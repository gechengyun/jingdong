<template>
  <div class="docker">
    <div :class="{ 'docker__item': true, 'docker__item--active': index === currentIndex }" class="docker__item" v-for="(item, index) in dockerList" :key="item.icon">
      <router-link :to='item.to'>
        <div class="iconfont" v-html="item.icon" />
        <div class="docker__title">{{item.text}}</div>
      </router-link>
    </div>
  </div>
</template>
<script>
export default {
  name: 'Docker',
  props: ['currentIndex'],
  setup () {
    const dockerList = [
      { icon: '&#xe67d;', text: '首页', to: { name: 'Home' } },
      { icon: '&#xe603;', text: '购物车', to: { name: 'CartList' } },
      { icon: '&#xe61e;', text: '订单', to: { name: 'OrderList' } },
      { icon: '&#xe609;', text: '我的', to: { name: 'Home' } },
      { icon: '&#12323;', text: '我发', to: { name: 'Other' } }
    ]
    return {
      dockerList
    }
  }
}
</script>
<style lang="scss" scoped>
@import '../style/viriables.scss';
@import '../style/mixins.scss';
.docker {
  display: flex;
  position: absolute;
  padding: 0 .18rem;
  box-sizing: border-box;
  left:0;
  bottom: 0;
  width:100%;
  height: .49rem;
  border-top:.01rem solid $content-bgcolor;
  color: $content-fontcolor;
  &__item {
    flex: 1;
    text-align: center;
    .iconfont {
      margin: .07rem 0 .02rem 0;
      font-size: .18rem;
    }
    a {
      color: $content-fontcolor;
      text-decoration: none;
    }
    &--active {
      a {
        color: #1fa4fc
      }
    }
  }
  &__title {
    font-size: .2rem;
    transform: scale(0.5, 0.5);
    transform-origin: center top;
  }
}
</style>
