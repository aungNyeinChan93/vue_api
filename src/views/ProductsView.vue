<template>
  <section>
    <h1>Products</h1>
    <div class="container">
      <table class="table table-bordered table-info table-hover">
        <thead>
          <tr class="table-primary">
            <th>ID</th>
            <th>Product</th>
            <th>Price</th>
            <th>Image</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="product in products" :key="product.id">
            <td>{{ product.id }}</td>
            <td>{{ product.category }}</td>
            <td>{{ product.price }}</td>
            <td>
              <img
                :src="product.image"
                class="image img-fluid rounded"
                alt=""
              />
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </section>
</template>

<script>
import axios from "axios";
import { onMounted, ref } from "vue";

export default {
  name: "productView",
  setup() {
    const products = ref([]);
    const error = ref(null);
    const getProducts = async () => {
      try {
        const response = await axios.get("http://localhost:4000/products");
        console.log(response);
        if (response.statusText != "OK") {
          throw new Error("request fail!");
        } else {
          products.value = response.data;
        }
      } catch (e) {
        error.value = e.message;
        console.log(error.value);
      }
    };

    onMounted(() => {
      getProducts();
    });

    return {
      products,
      error,
      getProducts,
    };
  },
};
</script>

<style scoped>
.image {
  width: 75px;
  height: 75px;
}
</style>
