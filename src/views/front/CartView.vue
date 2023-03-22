<template>
    <VueLoading :active="isLoading"></VueLoading>
    <div class="container">
        <div class="mt-4">
            <div class="text-end" v-if="cart.carts && cart.carts.length > 0">
                <button class="btn btn-outline-danger" type="button" @click="delCars">清空購物車</button>
            </div>
            <table class="table align-middle">
                <thead>
                <tr>
                    <th></th>
                    <th>品名</th>
                    <th style="width: 150px">數量/單位</th>
                    <th>單價</th>
                    <th>小計</th>
                </tr>
                </thead>
                <tbody>
                <template v-if="cart.carts && cart.carts.length > 0">
                    <tr v-for="cartItem in cart.carts" :key="cartItem.id">
                        <td>
                            <button type="button" class="btn btn-outline-danger btn-sm" @click="delCart(cartItem.id)"
                            :disabled="cartItem.id === loadingItem">
                            <i class="fas fa-spinner fa-pulse"  v-if="cartItem.id === loadingItem"></i>
                            x
                            </button>
                        </td>
                        <td>
                            {{ cartItem.product.title }}
                            <div class="text-success" v-if="cartItem.coupon">
                            已套用優惠券
                            </div>
                        </td>
                        <td>
                            <div class="input-group input-group-sm">
                            <div class="input-group mb-3">
                                <select class="form-select" v-model.number="cartItem.qty" @change="updateCart(cartItem)"
                                :disabled="cartItem.id === loadingItem">
                                <option v-for="i in 20" :value="i" :key="`${i}_qty`">{{i}}</option>
                                </select>
                                <span class="input-group-text" id="basic-addon2">{{ cartItem.product.unit }}</span>
                            </div>
                            </div>
                        </td>
                        <td class="text-end">
                            <small class="text-success" v-if="cartItem.product.price !== cartItem.product.origin_price">折扣價：</small>
                            {{ cartItem.product.price }}
                        </td>
                        <td class="text-end">
                            <small class="text-success" v-if="cart.final_total !== cart.total">折扣價：</small>
                            {{ cartItem.final_total }}
                        </td>
                    </tr>
                </template>
                <template v-else>
                    <tr>
                        <td colspan="5" class="text-center">目前購物車沒有商品</td>
                    </tr>
                </template>
                </tbody>
                <tfoot>
                <tr>
                    <td colspan="4" class="text-end">總計</td>
                    <td class="text-end">{{ cart.total }}</td>
                </tr>
                <tr v-if="cart.final_total !== cart.total">
                    <td colspan="4" class="text-end text-success">折扣價</td>
                    <td class="text-end text-success">{{ cart.final_total }}</td>
                </tr>
                </tfoot>
            </table>
        </div>
        <div class="my-5 row justify-content-center">
            <v-form ref="form" class="col-md-6" v-slot="{ errors }" @submit="createOrder">
                <div class="mb-3">
                    <label for="email" class="form-label">Email</label>
                    <v-field id="email" name="email" type="email" class="form-control"
                            :class="{ 'is-invalid': errors['email'] }" placeholder="請輸入 Email"
                            v-model="form.user.email" rules="email|required"></v-field>
                    <error-message name="email" class="invalid-feedback"></error-message>
                </div>

                <div class="mb-3">
                    <label for="name" class="form-label">收件人姓名</label>
                    <v-field id="name" name="姓名" type="text" class="form-control" :class="{ 'is-invalid': errors['姓名'] }"
                            placeholder="請輸入姓名" rules="required" v-model="form.user.name"></v-field>
                    <error-message name="姓名" class="invalid-feedback"></error-message>
                </div>

                <div class="mb-3">
                    <label for="tel" class="form-label">收件人電話</label>
                    <v-field id="tel" name="電話" type="tel" class="form-control" :class="{ 'is-invalid': errors['電話'] }"
                            placeholder="請輸入電話" v-model="form.user.tel" :rules="isPhone"></v-field>
                    <error-message name="電話" class="invalid-feedback"></error-message>
                </div>

                <div class="mb-3">
                    <label for="address" class="form-label">收件人地址</label>
                    <v-field id="address" name="地址" type="text" class="form-control" :class="{ 'is-invalid': errors['地址'] }"
                            placeholder="請輸入地址" rules="required" v-model="form.user.address"></v-field>
                    <error-message name="地址" class="invalid-feedback"></error-message>
                </div>

                <div class="mb-3">
                    <label for="message" class="form-label">留言</label>
                    <textarea id="message" name="message" class="form-control" cols="30" rows="10" v-model="form.message"></textarea>
                </div>
                <div class="text-end">
                    <button type="submit" class="btn btn-danger">送出訂單</button>
                </div>
            </v-form>
        </div>
    </div>
</template>

<script>
const { VITE_API, VITE_APIPATH } = import.meta.env
export default {
  data () {
    return {
      isLoading: false,
      loadingItem: '', // 存 ID
      cart: {},
      form: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: ''
        },
        message: ''
      }
    }
  },
  methods: {
    // 購物車列表
    getCart () {
      this.isLoading = true
      this.$http.get(`${VITE_API}/api/${VITE_APIPATH}/cart`)
        .then(res => {
          this.cart = res.data.data
          this.isLoading = false
        })
        .catch(err => {
          alert(err.response.data.message)
          this.isLoading = false
        })
    },
    // 刪除購物車項目（單一）
    delCart (id) {
      this.loadingItem = id
      this.$http.delete(`${VITE_API}/api/${VITE_APIPATH}/cart/${id}`)
        .then(res => {
          this.getCart()
          alert(res.data.message)
        })
        .catch(err => {
          alert(err.response.data.message)
        })
    },

    // 刪除購物車項目（全部）
    delCars () {
      this.$http.delete(`${VITE_API}/api/${VITE_APIPATH}/carts`)
        .then(res => {
          this.getCart()
          alert(res.data.message)
          this.loadingItem = ''
        })
        .catch(err => {
          alert(err.response.data.message)
        })
    },

    // 調整購物車產品數量
    updateCart (data) {
      this.loadingItem = data.id
      this.$http.put(`${VITE_API}/api/${VITE_APIPATH}/cart/${data.id}`, {
        data: {
          product_id: data.product_id,
          qty: data.qty
        }
      })
        .then(res => {
          this.getCart()
          alert(res.data.message)
          this.loadingItem = ''
        })
        .catch(err => {
          alert(err.response.data.message)
        })
    },

    // 結帳付款
    createOrder () {
      this.$http.post(`${VITE_API}/api/${VITE_APIPATH}/order`, { data: this.form })
        .then(res => {
          this.$refs.form.resetForm()
          this.form = {
            user: {
              name: '',
              email: '',
              tel: '',
              address: ''
            },
            message: ''
          }
          this.getCart()
          alert(res.data.message)
        })
        .catch(err => {
          alert(err.response.data.message)
        })
    },

    isPhone (value) {
      const phoneNumber = /^(09)[0-9]{8}$/
      return phoneNumber.test(value)
        ? true
        : '需要正確的電話號碼'
    }
  },
  mounted () {
    this.getCart()
  }
}
</script>