<template>
  <div>
    <div class="mask" v-if="showCart && calculations.total > 0" @click="handleCartShowChange"></div>
    <div class="cart">
      <div class="product" v-if="showCart && calculations.total > 0">
        <div class="product__header">
          <div class="product__header__all" @click="() => setCartItemsChecked(shopId)">
            <span class="product__header__icon iconfont" v-html="calculations.allchecked ? '&#xe70f;':'&#xe667;'"></span>
            全选
          </div>
          <div class="product__header__clear"><span class="product__header__clear__btn" @click="() => cleanCartProducts(shopId)">清空购物车</span></div>
        </div>
        <div class="product__item" v-for="item in productList" :key="item._id">
          <div
          class="product__item__checked iconfont"
          v-html="item.check? '&#xe70f;': '&#xe667;'"
          @click="()=>changeCartItemChecked(shopId, item._id)"
          >
          </div>
          <img class="product__item__img" :src="item.imgUrl" />
          <div class="product__item__detail">
            <h4 class="product__item__title">{{item.name}}</h4>
            <p class="product__item__price">
              <span class="product__item__yen">¥</span>{{item.price}}
              <span class="product__item__origin">¥{{item.oldPrice}}</span>
            </p>
          </div>
          <div class="product__number">
            <span class="product__number__minus" @click="() => {changeCartItemInfo(shopId, item._id, item, -1)}">-</span>
            {{item.count ||0}}
            <span class="product__number__plus" @click="() => {changeCartItemInfo(shopId, item._id, item, 1)}">+</span>
          </div>
        </div>
      </div>
      <div class="check">
        <div class="check__icon">
          <img src="http://www.dell-lee.com/imgs/vue3/basket.png"
          class="check__icon__img"
          @click="handleCartShowChange"
          />
          <div class="check__icon__tag">{{calculations.total}}</div>
        </div>
        <div class="check__info">
          总计：<span class="check__info__price">¥ {{calculations.price}}</span>
        </div>
        <div class="check__btn" v-show="calculations.total > 0">
          <router-link :to= "{ path: `/orderConfirmation/${shopId}` }">去结算</router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'
import { useStore } from 'vuex'
import { useRoute } from 'vue-router'
import { useCommonCartEffect } from '../../effects/cartEffect.js'
// 获取购物车信息逻辑
const useCartEffect = (shopId) => {
  const store = useStore()
  const { calculations, productList, changeCartItemInfo } = useCommonCartEffect(shopId)
  const changeCartItemChecked = (shopId, productId) => {
    store.commit('changeCartItemChecked', {
      shopId, productId
    })
  }
  const cleanCartProducts = () => {
    store.commit('cleanCartProducts', {
      shopId
    })
  }
  const setCartItemsChecked = (shopId) => {
    store.commit('setCartItemsChecked', {
      shopId
    })
  }
  return {
    calculations, productList, changeCartItemInfo, changeCartItemChecked, cleanCartProducts, setCartItemsChecked
  }
}

// 展示隐藏购物车逻辑
const toggleCartEffect = () => {
  const showCart = ref(false)
  const handleCartShowChange = () => {
    showCart.value = !showCart.value
  }
  return {
    showCart,
    handleCartShowChange
  }
}
export default {
  name: 'Cart',
  setup () {
    const route = useRoute()
    const shopId = route.params.id
    const { handleCartShowChange, showCart } = toggleCartEffect()
    const { calculations, productList, changeCartItemInfo, changeCartItemChecked, cleanCartProducts, setCartItemsChecked } = useCartEffect(shopId)
    return {
      calculations,
      productList,
      changeCartItemInfo,
      shopId,
      changeCartItemChecked,
      cleanCartProducts,
      setCartItemsChecked,
      showCart,
      handleCartShowChange
    }
  }
}
</script>
<style lang="scss" scoped>
@import '../../style/viriables';
@import '../../style/mixins';
.mask {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  top:0;
  z-index: 1;
  background: rgba(0, 0, 0, .5)
}
.cart {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 2;
  background: $bg-color;
}
.product {
  flex:1;
  overflow-y:scroll;
  background: $bg-color;
  &__header {
    display: flex;
    line-height: .52rem;
    border-bottom: .01rem solid $content-bgcolor;
    font-size: .14rem;
    color:$content-fontcolor;
    &__all {
      width: .64rem;
      margin-left:.16rem;
    }
    &__icon {
      display: inline-block;
      margin-right: .1rem;
      color: $btn-bgcolor;
      font-size: .2rem;
      vertical-align: top;
    }
    &__clear {
      flex: 1;
      margin-right:.16rem;
      text-align: right;
      &__btn {
        display: inline-block
      }
    }
  }
  &__item {
    position: relative;
    display: flex;
    padding:.12rem 0;
    margin: 0 .16rem;
    border-bottom: .01rem solid $content-bgcolor;
    &__checked {
      color: $btn-bgcolor;
      font-size: .2rem;
      line-height: .5rem;
      margin-right: .2rem;
    }
    &__detail {
      overflow: hidden
    }
    &__img {
      width: .46rem;
      height: .46rem;
      margin-right: .16rem;
    }
    &__title {
      margin: 0;
      line-height: .2rem;
      font-size: .14rem;
      color: $content-fontcolor;
      @include ellipse;
    }
    &__price {
      margin: .06rem 0 0 0;
      line-height: .2rem;
      font-size: .14rem;
      color: $highlight-fontcolor;

    }
    &__yen {
      font-size: .12rem;
    }
    &__origin {
      margin-left: .06rem;
      line-height: .2rem;
      font-size: .12rem;
      color:$light-fontcolor;
      text-decoration: line-through;
    }
    .product__number {
      position: absolute;
      right: 0rem;
      bottom: .26rem;
      &__minus,
      &__plus {
        display: inline-block;
        width: .2rem;
        height: .2rem;
        border-radius: 50%;
        font-size: .2rem;
        text-align: center;
        line-height: .16rem
      }
      &__minus {
        color: $medium-fontcolor;
        border: .01rem solid $medium-fontcolor;
        margin-right: .05rem;
      }
      &__plus {
        background: $btn-bgcolor;
        color:$bg-color;
        margin-left: .05rem;
      }
    }
  }
}
.check {
  display: flex;
  line-height: .49rem;
  height: .49rem;
  border-top:.01rem solid $content-bgcolor;
  &__icon {
    position: relative;
    width: .84rem;
    &__img {
      display: block;
      margin: .12rem auto;
      width: .28rem;
      height: .26rem;
    }
    &__tag {
      position: absolute;
      left: .46rem;
      top:.04rem;
      padding: 0 .04rem;
      min-width: .2rem;
      height: .2rem;
      border-radius: .1rem;
      background-color: $highlight-fontcolor;
      font-size: .12rem;
      line-height: .2rem;
      text-align: center;
      color:$bg-color;
      transform: scale(.5);
      transform-origin: left center;
    }
  }
  &__info {
    flex: 1;
    color:$content-fontcolor;
    font-size: .12rem;
    &__price {
      line-height: .49rem;
      color:$highlight-fontcolor;
      font-size: .18rem;
    }
  }
  &__btn {
    width: .98rem;
    background-color: #4FB0F9;
    font-size: .14rem;
    text-align: center;
    a {
      color: $bg-color;
      text-decoration: none
    }
  }
}
</style>
