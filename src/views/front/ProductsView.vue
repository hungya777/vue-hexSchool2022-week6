<template>
    <VueLoading :active="isLoading"></VueLoading>
    <div class="container">
        <div class="mt-4">
          <!-- 產品Modal -->
          <user-product-modal ref="userProductModal" :product="product" @add-cart="addCart"></user-product-modal>
          <!-- 產品Modal -->
          <!-- 產品列表 -->
          <table class="table align-middle">
            <thead>
              <tr>
                <th>圖片</th>
                <th>商品名稱</th>
                <th>價格</th>
                <th></th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="product in products" :key="product.id">
                <td style="width: 200px">
                  <div style="height: 100px; background-size: cover; background-position: center"
                    :style="{backgroundImage: `url(${product.imageUrl})`}"></div>
                </td>
                <td>
                  {{ product.title }}
                </td>
                <td>
                  <div class="h5" v-if="product.price === product.origin_price">{{ product.origin_price }} 元</div>
                  <div v-else>
                    <del class="h6">原價 {{ product.origin_price }} 元</del>
                    <div class="h5">現在只要 {{ product.price }} 元</div>
                  </div>
                </td>
                <td>
                  <div class="btn-group btn-group-sm">
                    <!-- <button type="button" class="btn btn-outline-secondary" @click="getProduct(product.id)"
                      :disabled="product.id === loadingItem">
                      <i class="fas fa-spinner fa-pulse" v-if="product.id === loadingItem"></i>
                      查看更多
                    </button> -->
                    <!-- 練習動態路由 -->
                    <router-link class="btn btn-outline-secondary" :to="`/product/${product.id}`">查看更多</router-link>
                    <button type="button" class="btn btn-outline-danger" @click="addCart(product.id)"
                      :disabled="product.id === loadingItem">
                      <i class="fas fa-spinner fa-pulse" v-if="product.id === loadingItem"></i>
                      加到購物車
                    </button>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
    </div>
</template>

<script>
import UserProductModal from '@/components/UserProductModal.vue'
const { VITE_API, VITE_APIPATH } = import.meta.env
export default {
  data () {
    return {
      products: [],
      product: {},
      loadingItem: '', // 存 ID
      isLoading: false
    }
  },
  methods: {
    // 產品列表
    getProducts () {
      this.isLoading = true
      this.$http.get(`${VITE_API}/api/${VITE_APIPATH}/products`)
        .then(res => {
          this.products = res.data.products
          this.isLoading = false
        })
        .catch(err => {
          alert(err.response.data.message)
          this.isLoading = false
        })
    },

    // 單一產品細節
    getProduct (id) {
      this.loadingItem = id
      this.$http.get(`${VITE_API}/api/${VITE_APIPATH}/product/${id}`)
        .then(res => {
          this.product = res.data.product
          this.$refs.userProductModal.showModal()
          this.loadingItem = ''
        })
        .catch(err => {
          alert(err.response.data.message)
        })
    },

    // 加入購物車
    addCart (productId, qty = 1) {
      this.loadingItem = productId
      this.$http.post(`${VITE_API}/api/${VITE_APIPATH}/cart`, {
        data: { product_id: productId, qty }
      })
        .then(res => {
          this.$refs.userProductModal.hideModal()
          alert(res.data.message)
          this.loadingItem = ''
        })
        .catch(err => {
          alert(err.response.data.message)
        })
    }
  },
  components: {
    UserProductModal
  },
  mounted () {
    this.getProducts()
  }
}
</script>