<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpense :income="+income" :expense="+expense" />
    <TransactionList :transactions="transactions" @transaction-deleted="handleTransactionDeleted" />
    <AddTransaction @transaction-submited="handleTransactionSubmitted" />
  </div>
</template>
<script setup>
import { computed, ref,onMounted } from 'vue';
import AddTransaction from './components/AddTransaction.vue';
import Balance from './components/Balance.vue';
import Header from './components/Header.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TransactionList from './components/TransactionList.vue';
import { useToast } from 'vue-toastification';
const toast = useToast()
const transactions = ref([]);
onMounted(()=>{
  const savedTransaction = JSON.parse(localStorage.getItem
  ('transactions'))
  if(savedTransaction){
    transactions.value = savedTransaction
  }
})
// Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0)
  // the last 0 provide the accumlator
  //  as 0 and loop through the array
})
// Get income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0).toFixed(2)
})
// Get Expense
const expense = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0).toFixed(2)
})
// Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  })
  saveTransactionToLocalStorage()
  toast.success('Transaction Added')
}
// generating unique id
const generateUniqueId = () => {
  return Math.floor(Math.random() * 10000)
}
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
  saveTransactionToLocalStorage()
  toast.success('Transaction Deleted')
}
// save to localstorage
const saveTransactionToLocalStorage = ()=>{
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}
</script>