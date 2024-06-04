<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expences="+expences" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";

import { useToast } from "vue-toastification";

import { ref, computed, onMounted } from "vue";

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem("transactions"));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

const total = computed(() => {
  return transactions.value
    .reduce((acc, item) => (acc += item.amount), 0)
    .toFixed(2);
});

const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, item) => (acc += item.amount), 0)
    .toFixed(2);
});

const expences = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, item) => (acc += item.amount), 0)
    .toFixed(2);
});

const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: transactions.value.length + 1,
    ...transactionData,
  });

  saveTransactions();

  toast.success("Transaction added successfully");
};

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );

  saveTransactions();

  toast.success("Transaction deleted successfully");
};

const saveTransactions = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>
