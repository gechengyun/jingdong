<template>
   <div>
    <div class="wrapper">
      购物车
    </div>
    <Docker :currentIndex="1"/>
  </div>
</template>

<script>
import Docker from '../../components/Docker'
export default {
  name: 'CartList',
  components: { Docker }
}
</script>

<style lang="scss" scoped>
@import '../../style/viriables.scss';
@import '../../style/mixins.scss';
.wrapper {
  overflow-y: auto;
  position: absolute;
  left:0;
  top:0;
  bottom:.5rem;
  right: 0;
  padding: 0 .18rem .2rem .18rem;
}
</style>
