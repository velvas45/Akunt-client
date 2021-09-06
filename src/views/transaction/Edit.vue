<template>
  <div class="container my-5">
    <div class="row justify-content-center">
      <div class="col-8">
        <router-link
          :to="{ name: 'transaction.index' }"
          class="btn btn-primary btn-sm rounded shadow mb-3"
          >Back</router-link
        >
        <div class="card rounded shadow">
          <div class="card-header">Edit Transaction</div>
          <div class="card-body">
            <form @submit.prevent="update">
              <div class="mb-3">
                <label class="form-label">Title</label>
                <input
                  type="text"
                  class="form-control"
                  v-model="transaction.title"
                />
                <div v-if="validation.title" class="text-danger">
                  {{ validation.title[0] }}
                </div>
              </div>
              <div class="mb-3">
                <label class="form-label">Amount</label>
                <input
                  type="number"
                  class="form-control"
                  v-model="transaction.amount"
                />
                <div v-if="validation.amount" class="text-danger">
                  {{ validation.amount[0] }}
                </div>
              </div>
              <div class="mb-3">
                <label class="form-label">Time</label>
                <input
                  type="text"
                  class="form-control"
                  placeholder="yyyy-mm-dd hh:mm:ss"
                  v-model="transaction.time"
                />
              </div>
              <div class="mb-3">
                <label class="form-label">Type</label>
                <select class="form-select" v-model="transaction.type">
                  <option value="expense">Expense</option>
                  <option value="revenue">Revenue</option>
                </select>
                <div v-if="validation.type" class="text-danger">
                  {{ validation.type[0] }}
                </div>
              </div>
              <button class="btn btn-outline-primary">Submit</button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { useRouter, useRoute } from 'vue-router';
import axios from 'axios';
import { onMounted, ref, reactive } from 'vue';

export default {
  setup() {
    // data binding
    let transaction = reactive({
      title: '',
      amount: '',
      time: '',
      type: '',
    });

    const validation = ref([]);

    const router = useRouter();
    const route = useRoute();

    onMounted(() => {
      // get data
      axios
        .get(`http://localhost:8000/api/transaction/${route.params.id}`)
        .then((res) => {
          const newResult = res.data.data;
          transaction.title = newResult.title;
          transaction.amount = newResult.amount;
          transaction.time = newResult.time;
          transaction.type = newResult.type;
        })
        .catch((err) => console.log(err.response));
    });

    function update() {
      axios
        .put(
          `http://localhost:8000/api/transaction/${route.params.id}`,
          transaction
        )
        .then((res) => {
          router.push({
            name: 'transaction.index',
          });
        })
        .catch((err) => {
          validation.value = err.response.data;
        });
    }

    return {
      transaction,
      validation,
      update,
    };
  },
};
</script>

<style scoped></style>
