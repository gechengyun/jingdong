<template>
  <div>
    <div class="order">
      <div class="order__price">实付金额 <b>¥{{calculations.price}}</b></div>
      <div class="order__btn" @click="() =>handleShowConfirmChange(true)">提交订单</div>
    </div>
    <div class="mask" v-show="showConfirm" @click="() =>handleShowConfirmChange(false)">
      <div class="mask__content" @click.stop>
        <h3 class="mask__content__title">确认要离开收银台？</h3>
        <p class="mask__content__desc">请尽快完成支付，否则将被取消</p>
        <div class="mask__content__btns">
          <div class="mask__content__btn mask__content__btn--first" @click="() => handleConfirmOrder(true)">取消订单</div>
          <div class="mask__content__btn mask__content__btn--last" @click="() => handleConfirmOrder(false)">确认支付</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useStore } from 'vuex'
import { post } from '../../utils/request'
import { useCommonCartEffect } from '../../effects/cartEffect.js'
// 下单相关逻辑
const useMakeOrderEffect = (productList, shopId, shopName) => {
  const router = useRouter()
  const store = useStore()
  const handleConfirmOrder = async (isCanceled) => {
    const products = []
    for (const i in productList.value) {
      const product = productList.value[i]
      products.push({ id: parseInt(product._id), num: product.count })
    }
    try {
      const result = await post('/api/order', {
        adderssId: 1,
        shopId,
        shopName: shopName.value,
        isCanceled,
        products
      })
      if (result && result.errno === 0) {
        store.commit('clearCartData', { shopId })
        router.push({ name: 'OrderList' })
      }
    } catch (e) {
      console.log('下单失败')
    }
  }
  return {
    handleConfirmOrder
  }
}
// 蒙层展示相关
const useShowMaskEffect = () => {
  const showConfirm = ref(false)
  const handleShowConfirmChange = (status) => {
    showConfirm.value = status
  }
  return { handleShowConfirmChange, showConfirm }
}
export default {
  name: 'Order',
  setup () {
    const route = useRoute()
    const shopId = parseInt(route.params.id)
    const { calculations, shopName, productList } = useCommonCartEffect(shopId)
    const { handleConfirmOrder } = useMakeOrderEffect(productList, shopId, shopName)
    const { handleShowConfirmChange, showConfirm } = useShowMaskEffect()
    return { handleShowConfirmChange, calculations, handleConfirmOrder, showConfirm }
  }
}
</script>
<style lang="scss" scoped>
@import '../../style/viriables';
.order {
  position: absolute;
  left:0;
  right:0;
  bottom:0;
  display: flex;
  height: .49rem;
  line-height: .49rem;
  background: $bg-color;
  &__price{
    flex: 1;
    text-indent: .24rem;
    font-size: .14rem;
    color: $content-fontcolor;
  }
  &__btn{
    width: .98rem;
    background: #4fb0f9;
    color: $bg-color;
    text-align: center;
    font-size: .14rem;
  }
}
.mask {
  z-index: 1;
  position: absolute;
  left:0 ;
  right:0;
  bottom:0;
  top:0;
  background: rgba(0,0,0,0.5);
  &__content {
    width: 3rem;
    height: 1.56rem;
    background: #fff;
    position: absolute;
    top:50%;
    left:50%;
    transform: translate(-50%, -50%);
    border-radius: .04rem;
    text-align: center;
    &__title {
      font-size: .18rem;
      color:#333;
      margin:.24rem 0 0 0;
      line-height: .26rem;
    }
    &__desc {
      color:#666;
      font-size: .14rem;
      margin:.08rem 0 0 0;
    }
    &__btns {
      display: flex;
      margin:.24rem .58rem;
    }
    &__btn {
      flex: 1;
      width: .8rem;
      line-height: .32rem;
      border-radius: .16rem;
      font-size: .14rem;
      &--first {
        margin-right:.12rem;
        border: .01rem solid #4fb0f9;
        color: #4fb0f9;
      }
      &--last {
        margin-left:.12rem;
        background: #4fb0f9;
        color: #fff;
      }
    }
  }
}
</style>
