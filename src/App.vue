<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpense :income="income" :expenses="expenses"/>
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>

<script setup>
import Balance from './components/Balance.vue';
import Header from "./components/Header.vue";
import IncomeExpense from './components/IncomeExpense.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import {useToast} from 'vue-toastification';

import { ref, computed, onMounted } from 'vue';

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransaction = JSON.parse(localStorage.getItem('transactions'));
  if(savedTransaction){
    transactions.value = savedTransaction;
  }
})

//Get Total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

// Get Income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) =>{
      return acc + transaction.amount;
    }, 0).toFixed(2);
});

//Get Expense
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) =>{
      return acc + transaction.amount;
    }, 0).toFixed(2);
});

//Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push ({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });
  savedTransactionsLocalStorage();
  toast.success('Transaction added');
};

//Genarate Unique ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
}

// Delect transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );


  toast.success('Transaction deleted.');
};

// Save to Localstorage
const savedTransactionsLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}
</script>

<style>

</style>