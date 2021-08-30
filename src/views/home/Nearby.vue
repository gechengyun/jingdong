<template>
  <div>
    <div class="nearby">
      <h3 class="nearby__title">附近店铺</h3>
      <router-link v-for="item in nearbyList" :key="item._id" :to="`/shop/${item._id}`">
        <ShopInfo :item="item" />
      </router-link>
    </div>
  </div>
</template>
<script>
import { ref } from 'vue'
import { get } from '../../utils/request'
import ShopInfo from '../../components/ShopInfo'
const useNearbyListEffect = () => {
  const nearbyList = ref([])
  const getNearbyList = async () => {
    const result = await get('/api/shop/hot-list')
    if (result && result.errno === 0) {
      nearbyList.value = result.data
    }
  }
  return {
    getNearbyList, nearbyList
  }
}
export default {
  name: 'Nearby',
  components: { ShopInfo },
  setup () {
    const { getNearbyList, nearbyList } = useNearbyListEffect()
    getNearbyList()
    return {
      nearbyList
    }
  }
}
</script>
<style lang="scss" scoped>
@import '../../style/viriables.scss';
@import '../../style/mixins.scss';
.nearby {
  &__title {
    margin: .16rem 0 .02rem 0;
    font-size: .18rem;
    color:$content-fontcolor;
    font-weight: normal;
  }
  a {
    text-decoration: none
  }
}
</style>
