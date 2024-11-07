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
          <small class="text-danger" v-show="validator.name"
            >Name Filed is Required!</small
          >
          <input
            type="number"
            placeholder="age"
            class="form-control my-2"
            v-model="customer.age"
          />
          <small class="text-danger" v-show="validator.age"
            >Age Filed is Required!</small
          >
          <br />
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

          <br />
          <div class="float-start">
            <small class="text-danger" v-show="validator.gender"
              >Gender Filed is Required!</small
            >
          </div>
          <input
            type="text"
            placeholder="address"
            class="form-control my-2"
            v-model="customer.address"
          />
          <small class="text-danger" v-show="validator.address"
            >Address Filed is Required!</small
          >

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
      <div class="row">
        <div class="col-12">
          <h4>
            <span v-show="customers.length > 1">Total Customers - </span>
            <span v-show="customers.length < 2">Total Customer - </span>
            <span class="text-danger fst-italic"> {{ customers.length }} </span>
          </h4>
        </div>
      </div>
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

    const validator = ref({
      name: false,
      age: false,
      gender: false,
      address: false,
    });

    const customer = ref({
      name: "",
      age: "",
      gender: "",
      address: "",
    });

    const customerCreate = async () => {
      try {
        validator.value.name = customer.value.name == "" ? true : false;
        validator.value.age = customer.value.age == "" ? true : false;
        validator.value.gender = customer.value.gender == "" ? true : false;
        validator.value.address = customer.value.address == "" ? true : false;

        if (
          customer.value.name != "" &&
          customer.value.age != "" &&
          customer.value.gender != "" &&
          customer.value.address != ""
        ) {
          const res = await axios.post(
            "http://localhost:4000/customers",
            customer.value
          );
          // getData();
          customers.value.push(res.data);
          clearFields();
        }
      } catch (error) {
        console.log(error.message);
      }
    };

    const clearFields = () => {
      customer.value.name = "";
      customer.value.age = "";
      customer.value.gender = "";
      customer.value.address = "";
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
          // customer.value.id = ++customers.value.length;
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
      clearFields,
      validator,
    };
  },
};
</script>
