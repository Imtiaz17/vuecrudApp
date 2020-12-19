<template>
  <b-modal v-model="isOpenModal">
    <div class="modal-card" style="width: auto">
      <header class="modal-card-head">
        <p class="modal-card-title">Add product</p>
        <button type="button" class="delete" @click="isOpenModal = false" />
      </header>
      <section class="modal-card-body">
        <b-field label="Title">
          <b-input v-model="form.title" type="text" placeholder="Product title">
          </b-input>
        </b-field>

        <b-field label="Description">
          <b-input
            v-model="form.description"
            type="textarea"
            placeholder="Description"
          >
          </b-input>
        </b-field>
        <b-field label="Price">
          <b-input
            v-model="form.price"
            type="number"
            placeholder="Product price"
          >
          </b-input>
        </b-field>
        <b-field class="file is-primary" :class="{ 'has-name': !!form.image }">
          <b-upload v-model="form.image" class="file-label">
            <span class="file-cta">
              <b-icon class="file-icon" icon="upload"></b-icon>
              <span class="file-label">Click to upload</span>
            </span>
            <span class="file-name" v-if="form.image">
              {{ form.image.name }}
            </span>
          </b-upload>
        </b-field>
      </section>
      <footer class="modal-card-foot">
        <button class="button" type="button" @click="isOpenModal = false">
          Close
        </button>
        <b-button :loading="uploading" type="is-success" @click="uploadProduct"
          >Submit</b-button
        >
      </footer>
    </div>
  </b-modal>
</template>

<script>
import Vue from "vue";
export default {
  data() {
    return {
      uploading: false,
      isOpenModal: false,
      form: {
        title: "",
        description: "",
        price: "",
        image: "",
      },
    };
  },
  mounted() {
    EventBus.$on("addPro", (data) => {
      this.isOpenModal = data;
    });
  },
  methods: {
    async uploadProduct() {
      this.uploading = true;
      let formData = new FormData();
      formData.append("title", this.form.title);
      formData.append("description", this.form.description);
      formData.append("price", this.form.price);
      formData.append("image", this.form.image);
      await this.$axios
        .post("product", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then((res) => {
          this.uploading = false;
          this.isModal = false;
          Vue.$toast.open({
            message: "Product uploaded successfully",
            type: "success",
          });
          this.isOpenModal = !this.isOpenModal;
          EventBus.$emit("uploaded", true);
        })
        .catch((error) => {
          this.uploading = false;
          this.isOpenModal = !this.isOpenModal;
          if (error.response.data.title) {
            Vue.$toast.open({
              message: error.response.data.title[0],
              type: "error",
            });
          }
          if (error.response.data.description) {
            Vue.$toast.open({
              message: error.response.data.description[0],
              type: "error",
            });
          }
          if (error.response.data.price) {
            Vue.$toast.open({
              message: error.response.data.price[0],
              type: "error",
            });
          }
          if (error.response.data.image) {
            Vue.$toast.open({
              message: error.response.data.image[0],
              type: "error",
            });
          }
        });
    },
  },
};
</script>

<style></style>
