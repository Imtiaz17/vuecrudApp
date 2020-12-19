<template>
  <div class="columns is-centered mt-5">
    <div class="column is-10">
      <div class="box">
        <b>Product List</b>
        <b-button type="is-info" style="float:right" @click="productAdd"
          >Add</b-button
        >
        <br />
        <br />
        <table
          class="table table is-bordered is-striped is-narrow is-hoverable is-fullwidth"
        >
          <thead>
            <tr>
              <th>Title</th>
              <th>Price</th>
              <th>Description</th>
              <th>Image</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(product, index) in products" :key="index">
              <td>{{ product.title }}</td>
              <td>{{ product.price }}</td>
              <td>{{ product.description }}</td>
              <td>
                <img :src="product.image" width="100" height="200" alt="" />
              </td>
              <td>
                <b-button
                  type="is-danger"
                  icon-right="delete"
                  @click="deleteProduct(product)"
                />
                <b-button
                  type="is-info"
                  icon-right="pencil"
                  @click="editProduct(product)"
                />
                <b-button
                  type="is-primary"
                  icon-right="eye"
                  @click="showSingle(product.id)"
                />
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <show-product :data="singleProduct" />
      <edit-product :data="singleProduct" />
      <add-product />
    </div>
  </div>
</template>

<script>
import showProduct from "@/components/show";
import addProduct from "@/components/add";
import editProduct from "@/components/edit";
import Vue from "vue";
export default {
  components: {
    showProduct,
    addProduct,
    editProduct,
  },
  name: "home",
  data() {
    return {
      singleProduct: {},
      products: [],
    };
  },
  created() {
    this.getProducts();
    if (!localStorage.getItem("token")) {
        this.$router.push('/')
    }
  },
  mounted() {
    EventBus.$on("uploaded", (data) => {
      if ((data == true)) {
        this.getProducts();
      }
    });
  },
  methods: {
    async getProducts() {
      this.isLoading = true;
      await this.$axios.get("/product").then((res) => {
        this.isLoading = false;
        this.products = res.data.data;
      });
    },
    deleteProduct(p) {
      this.$buefy.dialog.alert({
        title: "Are you Sure to delete this product?",
        message: "This can not be undone",
        cancelText: "No",
        canCancel: true,
        confirmText: "Yes",
        onConfirm: () => this.productDelete(p),
      });
    },
    async productDelete(p) {
      await this.$axios
        .delete(`/product/${p.id}`)
        .then((res) => {
          Vue.$toast.open({
            message: "Product deleted successfully",
            type: "success",
          });
          this.getProducts();
        })
        .catch((error) => {
          Vue.$toast.open({
            message: "Server error",
            type: "error",
          });
        });
    },
    async showSingle(pid) {
      await this.$axios
        .get(`/product/${pid}`)
        .then((res) => {
          this.singleProduct = res.data.data;
          EventBus.$emit("open", true);
        })
        .catch((error) => {
          Vue.$toast.open({
            message: "Server error",
            type: "error",
          });
        });
    },
    productAdd() {
      EventBus.$emit("addPro", true);
    },
    editProduct(p) {
      EventBus.$emit("editPro", true);
      this.singleProduct = p;
    },
  },
};
</script>

<style scoped>
.button {
  margin-right: 5px;
}
</style>
