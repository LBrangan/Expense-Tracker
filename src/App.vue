<template>
  <header class="card-header">
    <h1>Expense Tracker</h1>
  </header>

  <div class="container">
    <div class="card-container">
      <div class="card">
        <div class="info-text">
          <Balance :total="total" /> <IncomeExpenses :income="+income" :expenses="+expenses" />
          <TransactionList
            :transactions="transactions"
            @transactionDeleted="handleTransactionDeleted"
          />
          <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'

import { useToast } from 'vue-toastification'

import { ref, computed, onMounted } from 'vue'

const toast = useToast()
const transactions = ref([])

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'))

  if (savedTransactions) {
    transactions.value = savedTransactions
  }
})

// Get Total
const total = computed(() => {
  return transactions.value
    .reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0)
    .toFixed(2)
})

//Get Income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0)
    .toFixed(2)
})

//Get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0)
    .toFixed(2)
})

//Add transactions
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  })
  saveTransactionsToLocalStorage()

  toast.success('Transaction Added!')
}

//Generate Unique ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 100000)
}

//Delete Transaction
const handleTransactionDeleted = (id) => {
  console.log(id)
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
  saveTransactionsToLocalStorage()

  toast.success('Transaction Deleted!')
}

//Save to Local Strage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}
</script>
