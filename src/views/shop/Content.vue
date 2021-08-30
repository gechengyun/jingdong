<template>
  <div class="content">
    <div class="category">
      <div :class="{'category__item':true, 'category__item--active': currentTab === item.tab}" @click="()=>handleTabClick(item.tab)" v-for="item in categories" :key="item.tab">{{item.name}}</div>
    </div>
    <div class="product">
      <div class="product__item" v-for="item in list" :key="item._id">
        <img class="product__item__img" :src="item.imgUrl" />
        <div class="product__item__detail">
          <h4 class="product__item__title">{{item.name}}</h4>
          <p class="product__item__sales">月售 {{item.sales}} 件</p>
          <p class="product__item__price">
            <span class="product__item__yen">¥</span>{{item.price}}
            <span class="product__item__origin">¥{{item.oldPrice}}</span>
          </p>
        </div>
        <div class="product__number">
          <span class="product__number__minus" @click="() => {changeCartItem(shopId, item._id, item, -1, shopName)}">-</span>
          {{getProductCartCount(shopId, item._id)}}
          <span class="product__number__plus" @click="() => {changeCartItem(shopId, item._id, item, 1, shopName)}">+</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { reactive, toRefs, ref, watchEffect } from 'vue'
import { useRoute } from 'vue-router'
import { useStore } from 'vuex'
import { get } from '../../utils/request'
import { useCommonCartEffect } from '../../effects/cartEffect.js'
const categories = [{ name: '全部商品', tab: 'all' }, { name: '秒杀', tab: 'seckill' }, { name: '新鲜水果', tab: 'fruit' }]
// 和tab切换相关的逻辑
const useTabEffect = () => {
  const currentTab = ref(categories[0].tab)
  const handleTabClick = (tab) => {
    currentTab.value = tab
  }
  return {
    currentTab, handleTabClick
  }
}
// 列表内容相关的逻辑
const useCurrentListEffect = (currentTab, shopId) => {
  const content = reactive({ list: [] })
  const getContentData = async () => {
    const result = await get(`/api/shop/${shopId}/products`, { tab: currentTab.value })
    if (result.errno === 0 && result.data.length) {
      content.list = result.data
    }
  }
  watchEffect(() => { getContentData() })
  const { list } = toRefs(content)
  return { list }
}

const useCartEffect = () => {
  const store = useStore()
  const { cartList, changeCartItemInfo } = useCommonCartEffect()
  const changeShopName = (shopId, shopName) => {
    store.commit('changeShopName', { shopId, shopName })
  }
  const changeCartItem = (shopId, productId, item, num, shopName) => {
    changeCartItemInfo(shopId, productId, item, num)
    changeShopName(shopId, shopName)
  }
  const getProductCartCount = (shopId, productId) => {
    return (cartList && cartList[shopId] && cartList[shopId].productList && cartList[shopId].productList[productId] && cartList[shopId].productList[productId].count) || 0
  }
  return {
    cartList, changeCartItem, getProductCartCount
  }
}
export default {
  name: 'Content',
  props: ['shopName'],
  setup () {
    const route = useRoute()
    const shopId = route.params.id
    const { currentTab, handleTabClick } = useTabEffect()
    const { list } = useCurrentListEffect(currentTab, shopId)
    const { cartList, changeCartItem, getProductCartCount } = useCartEffect()
    return { shopId, changeCartItem, cartList, handleTabClick, categories, currentTab, list, getProductCartCount }
  }
}
</script>

<style lang="scss" scoped>
@import '../../style/viriables';
@import '../../style/mixins';
.content {
  display: flex;
  position: absolute;
  left:0;
  right:0;
  top:1.5rem;
  bottom:.5rem;
}
.category {
  width: .76rem;
  background: $search-bgColor;
  height: 100%;
  overflow-y:scroll;
  &__item {
    line-height: .4rem;
    text-align: center;
    font-size:.14rem;
    color: $content-fontcolor;
    &--active {
      background: $bg-color
    }
  }
}
.product {
  flex:1;
  overflow-y:scroll;
  &__item {
    position: relative;
    display: flex;
    padding:.12rem 0;
    margin: 0 .16rem;
    border-bottom: .01rem solid $content-bgcolor;
    &__detail {
      overflow: hidden
    }
    &__img {
      width: .68rem;
      height: .68rem;
      margin-right: .16rem;
    }
    &__title {
      margin: 0;
      line-height: .2rem;
      font-size: .14rem;
      color: $content-fontcolor;
      @include ellipse;
    }
    &__sales {
      margin: .06rem 0;
      font-size: .12rem;
      color: $content-fontcolor;
    }
    &__price {
      margin: 0;
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
      bottom: .12rem;
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
</style>
