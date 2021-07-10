<template>
  <div class="mt-4">
    <h1 class="mb-3">產品列表</h1>
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
        <tr v-for="item in products" :key="item.id">
          <td style="width: 200px">
            <div style="height: 100px; background-size: cover; background-position: center"
              :style="{ 'background-image' : `url(${item.imageUrl})` }">
            </div>
          </td>
          <td>
            {{ item.title }}
          </td>
          <td>
            <div class="h5">{{ item.price }} 元</div>
            <del class="h6">原價 {{ item.origin_price }} 元</del>
            <div class="h5">現在只要 {{ item.price }} 元</div>
          </td>
          <td>
            <div class="btn-group btn-group-sm">
              <button type="button" class="btn btn-outline-secondary" @click="getProduct(item.id)"
                :disabled="loadingStatus.loadingItem === item.id">
                <i class="fas fa-spinner fa-pulse" v-if="loadingStatus.loadingItem === item.id"></i>
                查看更多
              </button>
              <button type="button" class="btn btn-outline-danger" @click="addCart(item.id)"
                :disabled="loadingStatus.loadingItem === item.id">
                <i class="fas fa-spinner fa-pulse" v-if="loadingStatus.loadingItem === item.id"></i>
                加到購物車
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
// import ProductInfoModal from '@/components/ProductInfoModal.vue';

export default {
  name: 'Products',
  data() {
    return {
      products: [],
      loadingStatus: {
        loadingItem: '',
      },
      product: {},
    };
  },
  components: {
    // ProductInfoModal,
  },
  created() {
    this.getProducts();
  },
  methods: {
    getProducts() {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products`;
      this.$http.get(url).then((res) => {
        if (res.data.success) {
          this.products = res.data.products;
        } else {
          // alert(res.data.message);
        }
      }).catch((err) => {
        console.log(err);
      });
    },
    getProduct(id) {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/product/${id}`;
      this.loadingStatus.loadingItem = id;
      this.$http.get(url).then((res) => {
        if (res.data.success) {
          this.product = res.data.product;
          this.loadingStatus.loadingItem = '';
          this.$refs.productInfoModal.openModal();
        } else {
          // alert(res.data.message);
        }
      }).catch((err) => {
        console.log(err);
      });
    },
    addCart(id, qty = 1) {
      this.loadingStatus.loadingItem = id;
      const url = `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart`;
      const cart = {
        product_id: id,
        qty,
      };
      this.$http
        .post(url, { data: cart })
        .then((res) => {
          if (res.data.success) {
            this.loadingStatus.loadingItem = '';
            this.$refs.productInfoModal.hideModal();
          } else {
            // alert(res.data.message);
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>
