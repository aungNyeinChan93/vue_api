<template>
  <section>
    <h1>Test</h1>
    <!-- Form -->
    <div class="row">
      <div class="col-6 offset-3 p-3">
        <form @submit.prevent="customerCreate">
          <input
            type="text"
            placeholder="name"
            class="form-control my-2"
            v-model="customer.name"
          />
          <input
            type="number"
            placeholder="age"
            class="form-control my-2"
            v-model="customer.age"
          />
          <div class="float-start my-2">
            <input
              type="radio"
              value="male"
              name="gender"
              v-model="customer.gender"
            />
            Male
            <input
              type="radio"
              value="female"
              name="gender"
              v-model="customer.gender"
            />
            Female
          </div>
          <input
            type="text"
            placeholder="address"
            class="form-control my-2"
            v-model="customer.address"
          />
          <button class="btn btn-sm btn-info my-2 d-flex">Submit</button>
        </form>
      </div>
    </div>
    <!-- Error -->
    <div class="row" v-if="error">
      <div class="col-6 offset-3">
        <div class="alert alert-danger">
          {{ error }}
        </div>
      </div>
    </div>
    <!-- success -->
    <div class="row" v-else>
      <!-- is loading -->
      <div class="col-6 offset-3" v-if="!customers.length">
        <div class="alert alert-danger">
          <h4>Loading ...!</h4>
        </div>
      </div>
      <!-- data have -->
      <div class="col-md-6 offset-3" v-if="customers.length">
        <table class="table table-bordered table-info table-hover">
          <thead class="">
            <tr class="">
              <th>Name</th>
              <th>Age</th>
              <th>Gender</th>
              <th>Address</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="customer in customers" :key="customer.id">
              <td>{{ customer.name }}</td>
              <td>{{ customer.age }}</td>
              <td>{{ customer.gender }}</td>
              <td>{{ customer.address }}</td>
              <td>
                <button
                  class="btn btn-sm btn-danger"
                  @click="deleteCustomer(customer.id)"
                >
                  Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </section>
</template>

<script>
import { onMounted, ref } from "vue";
import axios from "axios";

export default {
  name: "TestView",
  setup() {
    const customers = ref([]);
    const error = ref(null);

    const customer = ref({
      name: null,
      age: null,
      gender: null,
      address: null,
    });

    const customerCreate = async () => {
      try {
        if (customer.value != "") {
          const res = await axios.post(
            "http://localhost:4000/customers",
            customer.value
          );
          customers.value.push(res.data);
        }
        customer.value = "";
      } catch (error) {
        console.log(error.message);
      }
    };

    const deleteCustomer = async (id) => {
      try {
        await axios.delete(`http://localhost:4000/customers/${id}`);
        customers.value = customers.value.filter((c) => c.id != id);
      } catch (error) {
        console.log(error.message);
      }
    };

    const getData = async () => {
      try {
        const response = await axios.get("http://localhost:4000/customers");
        if (response.status == "200") {
          customers.value = await response.data;
        } else {
          throw Error("request is bad ");
        }
      } catch (e) {
        error.value = e.message;
        console.log(error.value);
      }
    };

    onMounted(() => {
      getData();
    });

    return {
      customers,
      getData,
      error,
      customer,
      customerCreate,
      deleteCustomer,
    };
  },
};
</script>
