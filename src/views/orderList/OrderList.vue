<template>
  <div>
    <div class="wrapper">
      <div class="title">我的订单</div>
      <div class="orders">
        <div class="order" v-for="(item, index) in list" :key="index">
          <div class="order__title">
            {{item.shopName}}
            <span class="order__status">{{item.isCanceled?'已取消': '已下单'}}</span>
          </div>
          <div class="order__content">
            <div class="order__content__imgs">
              <img v-for="(innerItem, index) in item.products" :key="index" class="order__content__img" :src="innerItem.product.img" />
            </div>
            <div class="order__content__info">
              <div class="order__content__price">¥{{item.totalPrice}}</div>
              <div class="order__content__count">共{{item.totalNum}}件</div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <Docker :currentIndex="2"/>
  </div>
</template>
<script>
import { reactive, toRefs } from 'vue'
import { get } from '../../utils/request'
import Docker from '../../components/Docker'
// 处理订单列表逻辑
const useOrderListEffect = () => {
  const data = reactive({ list: [] })
  const getNearbyList = async () => {
    const result = await get('/api/order')
    if (result && result.errno === 0) {
      const orderList = result.data
      orderList.forEach((order) => {
        const products = order.products || []
        let totalPrice = 0
        let totalNum = 0
        products.forEach((productItem) => {
          totalPrice += (productItem.product.price * productItem.orderSales) || 0
          totalNum += (productItem.orderSales || 0)
        })
        order.totalPrice = totalPrice
        order.totalNum = totalNum
      })
      data.list = result.data
    }
  }
  getNearbyList()
  const { list } = toRefs(data)
  return {
    list
  }
}
export default {
  name: 'OrderList',
  components: { Docker },
  setup () {
    const { list } = useOrderListEffect()
    console.log(list)
    return { list }
  }
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
  background: rgb(248,248,248);
}
.title {
  line-height: .44rem;
  background: $bg-color;
  color: $content-fontcolor;
  font-size: .16rem;
  text-align: center;
}
.order {
  margin: .16rem .18rem;
  background: $bg-color;
  padding: .16rem;
  &__title {
    font-size: .16rem;
    color: $content-fontcolor;
    line-height: .22rem;
    margin-bottom: .16rem;
  }
  &__status {
    float: right;
    font-size: .14rem;
    color: $light-fontcolor;
  }
  &__content {
    display: flex;
    &__imgs {
      flex: 1
    }
    &__img{
      width: .4rem;
      height: .4rem;
      margin-right: .12rem;
    }
    &__info {
      width: .7rem;
    }
    &__price {
      line-height: .2rem;
      font-size: .14rem;
      color: $highlight-fontcolor;
      text-align: right;
      margin-bottom: .04rem;
    }
    &__count {
      line-height: .14rem;
      font-size: .12rem;
      color: $content-fontcolor;
      text-align: right;
    }
  }
}
</style>
