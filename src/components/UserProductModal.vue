<template>
    <div class="modal fade" id="UserProductModal" tabindex="-1" role="dialog"
          aria-labelledby="exampleModalLabel" aria-hidden="true" ref="modal">
        <div class="modal-dialog modal-xl" role="document">
          <div class="modal-content border-0">
            <div class="modal-header bg-dark text-white">
              <h5 class="modal-title" id="exampleModalLabel">
                <span>{{ product.title }}</span>
              </h5>
              <button type="button" class="btn-close"
                      data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
              <div class="row">
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
                    <!-- <input type="number" class="form-control"
                          min="1" v-model.number="qty"> -->
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
          </div>
        </div>
    </div>
</template>

<script>
import Modal from 'bootstrap/js/dist/modal'
export default {
  props: ['product'],
  data () {
    return {
      modal: '',
      qty: 1
    }
  },
  methods: {
    showModal () {
      this.qty = 1
      this.modal.show()
    },
    hideModal () {
      this.modal.hide()
    },
    addCart () {
      this.$emit('addCart', this.product.id, this.qty)
    }
  },
  mounted () {
    // dom 生成後，再取得 model
    this.modal = new Modal(this.$refs.modal, { keyboard: false })
  }
}
</script>