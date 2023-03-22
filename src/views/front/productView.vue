<template>
  <VueLoading :active="isLoading"></VueLoading>
  <div class="container mt-4">
    <h2 class="text-center">
      {{ product.title }}
    </h2>
    <div class="row mt-4">
      <div class="col-sm-6">
          <img class="img-fluid" :src="product.imagesUrl" :alt="product.title">
      </div>
      <div class="col-sm-6">
        <span class="badge bg-primary rounded-pill">{{ product.category }}</span>
        <p>商品描述：{{ product.description }}</p>
        <p>商品內容：{{ product.content }}</p>
        <div class="h5" v-if="product.price == product.origin_price">{{ product.origin_price }} 元</div>
        <div v-else>
          <del class="h6">原價 {{ product.origin_price }} 元</del>
          <div class="h5">現在只要 {{ product.price }} 元</div>
        </div>
          <div>
            <div class="input-group">
              <select class="form-select" v-model.number="qty">
                <option v-for="i in 20" :value="i" :key="`${i}_qty`">{{i}}</option>
              </select>
              <button type="button" class="btn btn-primary" @click="addCart">加入購物車</button>
          </div>
        </div>
      </div>
        <!-- col-sm-6 end -->
    </div>
  </div>
</template>

<script>
const { VITE_API, VITE_APIPATH } = import.meta.env
export default {
  data () {
    return {
      product: {},
      qty: 1,
      isLoading: false
    }
  },
  methods: {
    // 單一產品細節
    getProduct () {
      this.isLoading = true
      const { id } = this.$route.params
      this.$http.get(`${VITE_API}/api/${VITE_APIPATH}/product/${id}`)
        .then(res => {
          this.product = res.data.product
          this.isLoading = false
        })
        .catch(err => {
          alert(err.response.data.message)
          this.isLoading = false
        })
    },

    // 加入購物車
    addCart () {
      this.isLoading = true
      const { id } = this.$route.params
      this.$http.post(`${VITE_API}/api/${VITE_APIPATH}/cart`, {
        data: { product_id: id, qty: this.qty }
      })
        .then(res => {
          alert(res.data.message)
          this.isLoading = false
        })
        .catch(err => {
          alert(err.response.data.message)
          this.isLoading = false
        })
    }
  },
  mounted () {
    this.getProduct()
  }
}
</script>