<template>
  <div class="products">
    <div class="products__title">{{shopName}}</div>
    <div class="products__wrapper">
      <div class="products__list">
        <div class="products__item" v-for="item in productList" :key="item._id">
          <img class="products__item__img" :src="item.imgUrl" />
          <div class="products__item__detail">
            <h4 class="products__item__title">{{item.name}}</h4>
            <p class="products__item__price">
              <span>
                <span class="products__item__yen">¥ </span>
                {{item.price}} x {{item.count}}
              </span>
              <span class="products__item__total">
                <span class="products__item__yen">¥ </span>
                {{(item.price * item.count).toFixed(2)}}
              </span>
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { useRoute } from 'vue-router'
import { useCommonCartEffect } from '../../effects/cartEffect.js'
export default {
  name: 'ProductList',
  setup () {
    const route = useRoute()
    const shopId = route.params.id
    const { calculations, shopName, productList } = useCommonCartEffect(shopId)
    return { calculations, shopName, productList }
  }
}
</script>
<style lang="scss" scoped>
@import '../../style/viriables';
@import '../../style/mixins';
.products {
  margin: .16rem .18rem .1rem .18rem;
  background: $bg-color;
  &__title {
    padding: .16rem;
    color: $content-fontcolor;
    font-size: .16rem;
  }
  &__wrapper {
    position: absolute;
    left:0;
    right:0;
    bottom:.6rem;
    top:2.6rem;
    overflow-y: scroll;
    margin:0 .18rem;
  }
  &__list {
    background: $bg-color;
  }
  &__item {
    position: relative;
    display: flex;
    padding: 0rem .16rem .16rem .16rem;
    &__img {
      width: .46rem;
      height: .46rem;
      margin-right: .16rem;
    }
    &__detail {
      flex: 1;
    }
    &__title {
      margin: 0;
      line-height: .2rem;
      font-size: .14rem;
      color: $content-fontcolor;
      @include ellipse;
    }
    &__price {
      display: flex;
      margin: .06rem 0 0 0;
      line-height: .2rem;
      font-size: .14rem;
      color: $highlight-fontcolor;
    }
    &__total {
      text-align: right;
      flex: 1;
      color: $dark-fontColor;
    }
    &__yen {
      font-size: .12rem;
    }
  }
}
</style>
