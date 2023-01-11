<template>
  <div class="paginations--wrapper">
    <Pagination
      :data="transactions"
      :total-pages="Math.ceil(transactions.length / perPage)"
      :total="transactions.length"
      :per-page="perPage"
      :current-page="currentPage"
      @pagechanged="onPageChange"
    ></Pagination>
  </div>
</template>

<script setup>
// import { transactions } from "../utils/db.json";
import Pagination from "../components/PaginationView.vue";
// import { onMounted } from "vue";
import { useStore } from "vuex";
import { computed, ref } from "vue";

const store = useStore();

const transactions = computed(() => store.getters.transactions);

var currentPage = ref(1);
var perPage = ref(4);

// onMounted(async () => {
//   await store.dispatch("setTransactions");
// });

const onPageChange = (page) => {
  currentPage.value = page;
};
</script>

<style scoped>
body {
  font-family: "Roboto";
  font-size: 16px;
  color: #505050;
}

.paginations--wrapper {
  background-color: #fafafa;
  max-width: 100%;
  height: 21rem;
  margin: 0 auto;
  margin-top: 3rem;
  border-radius: 1rem;
  padding: 1rem;
}
</style>
