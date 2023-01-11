<template>
  <div class="paginations">
    <h2>Transactions</h2>
    <div class="head">
      <div class="row">
        <div class="col">id</div>
        <div class="col">date</div>
        <div class="col">amount</div>
        <div class="col">type</div>
      </div>
    </div>
    <hr />
    <div class="items">
      <div
        class="row"
        v-for="(transaction, index) in paginatedData"
        :key="index"
      >
        <div class="col">{{ transaction.id }}</div>
        <div class="col">{{ transaction.date }}</div>
        <div class="col">${{ transaction.amount }}</div>
        <div class="col" v-if="transaction.type === 'success'">
          <span style="color: green">{{ transaction.type }}</span>
        </div>
        <div class="col" v-if="transaction.type === 'error'">
          <span style="color: red">{{ transaction.type }}</span>
        </div>
        <div class="col" v-if="transaction.type === 'processing'">
          <span style="color: silver">{{ transaction.type }}</span>
        </div>
      </div>
    </div>

    <ul class="pagination" v-if="data.length > perPage + 1 || currentPage > 1">
      <li class="pagination-item" title="First page">
        <button
          type="button"
          @click="onClickFirstPage"
          :disabled="isInFirstPage"
        >
          &#10094;&#10094;&laquo;
        </button>
      </li>

      <li class="pagination-item" title="Previous page">
        <button
          type="button"
          @click="onClickPreviousPage"
          :disabled="isInFirstPage"
        >
          &#10094;&lsaquo;
        </button>
      </li>

      <li class="pagination-item" v-for="(page, index) in pages" :key="index">
        <button
          type="button"
          @click="onClickPage(page.number)"
          :disabled="page.isDisabled"
          :class="{ active: isPageActive(page.number) }"
        >
          {{ page.number }}
        </button>
      </li>

      <li class="pagination-item" title="Next page">
        <button type="button" @click="onClickNextPage" :disabled="isInLastPage">
          &#10095;&rsaquo;
        </button>
      </li>
      <li class="pagination-item" title="Last page">
        <button type="button" @click="onClickLastPage" :disabled="isInLastPage">
          &#10095;&#10095;&raquo;
        </button>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  data: {
    type: Array,
    required: true,
  },
  maxVisibleButtons: {
    type: Number,
    required: false,
    default: 3,
  },
  totalPages: {
    type: Number,
    required: true,
  },
  total: {
    type: Number,
    required: true,
  },
  perPage: {
    type: Number,
    required: true,
  },
  currentPage: {
    type: Number,
    required: true,
  },
});

const emit = defineEmits(["pagechanged"]);

const paginatedData = computed(() => {
  let start = (props.currentPage - 1) * props.perPage,
    end = start + props.perPage;
  return props.data.slice(start, end);
});

const startPage = computed(() => {
  if (props.currentPage === 1) return 1;
  if (props.currentPage === props.totalPages)
    return (
      props.totalPages - props.maxVisibleButtons + (props.maxVisibleButtons - 1)
    );
  return props.currentPage - 1;
});

const endPage = computed(() => {
  return Math.min(
    startPage.value + props.maxVisibleButtons - 1,
    props.totalPages
  );
});

const pages = computed(() => {
  let range = [];
  for (let i = startPage.value; i <= endPage.value; i++) {
    range.push({
      number: i,
      isDisabled: i === props.currentPage,
    });
  }
  return range;
});

const isInFirstPage = computed(() => {
  return props.currentPage === 1;
});

const isInLastPage = computed(() => {
  return props.currentPage === props.totalPages;
});

const onClickFirstPage = () => {
  emit("pagechanged", 1);
};

const onClickPreviousPage = () => {
  emit("pagechanged", props.currentPage - 1);
};

const onClickPage = (page) => {
  emit("pagechanged", page);
};

const onClickNextPage = () => {
  emit("pagechanged", props.currentPage + 1);
};

const onClickLastPage = () => {
  emit("pagechanged", props.totalPages);
};

const isPageActive = (page) => {
  return props.currentPage === page;
};
</script>

<style scoped>
.paginations {
  display: flex;
  height: calc(100% - 1.25rem);
  flex-direction: column;
}
hr {
  border: 1px solid silver;
  width: 100%;
}
h2 {
  font-size: 1.5rem;
  text-align: center;
  margin-top: 0;
}
.row {
  display: flex;
  align-items: center;
  padding: 0;
  margin: 0.75rem 0;
}
.col {
  justify-content: center;
  flex-basis: 25%;
  display: inline-flex;
}
.pagination {
  display: flex;
  justify-content: center;
  padding: 0;
  margin: auto 0 0 0;
  list-style-type: none;
}
.pagination-item button {
  margin: 0 !important;
  padding: 0.25rem 0.5rem;
  font-size: 1.1rem;
  border: none;
  border-radius: 0.25rem;
  background: none;
}
.pagination-item button:hover {
  cursor: pointer;
  background-color: silver;
}
.pagination-item button[disabled="disabled"] {
  color: silver;
  cursor: default;
}
.pagination-item button[disabled="disabled"]:hover {
  cursor: default;
  cursor: not-allowed;
  background-color: transparent;
}
.pagination-item button.active {
  color: red;
}
</style>
