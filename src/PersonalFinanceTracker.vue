<template>
  <div class="min-h-screen bg-gray-100 p-8">
    <div class="max-w-4xl mx-auto">
      <h1 class="text-4xl font-bold text-center mb-8">Personal Finance Tracker</h1>
      <p class="text-center text-gray-600 mb-10">Track income, expenses &amp; budget</p>

      <!-- Add transaction Form  -->
      <div class="bg-white p-6 rounded-2xl shadow-md mb-8">
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
          <input
            v-model="newDesc"
            placeholder="Description (e.g. Salary, Groceties)"
            class="border border-gray-300 p-3 rounded-xl focus:outline-none focus:border-blue-500"
          />

          <input
            type="number"
            placeholder="Amount"
            v-model="newAmount"
            class="border border-gray-300 p-3 rounded-xl focus:outline-none focus:border-blue-500"
          />

          <!-- Add Status transaction -->
          <select
            v-model="newType"
            class="border border-gray-300 p-3 rounded-xl focus:outline-none focus:border-blue-500"
          >
            <option value="income">Income (+)</option>
            <option value="expense">Expense (-)</option>
          </select>

          <button
            @click="addTransaction"
            class="bg-blue-600 hover:bg-blue-700 text-white font-medium py-3 rounded-xl transition-colors"
          >
            Add Transaction
          </button>
        </div>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-8">
        <div class="bg-white p-5 rounded-2xl shadow text-center">
          <p class="text-gray-500 text-sm">Balance</p>
          <p class="text-2xl font-bold mt-1" :class="{ 'text-red-500': balance < 0 }">
            ${{ balance.toFixed(2) }}
          </p>
        </div>

        <div class="bg-white p-5 rounded-2xl shadow text-center">
          <p class="text-gray-500 text-sm">Total Income</p>
          <p class="text-2xl font-semibold text-green-600 mt-1">+${{ totalIncome.toFixed(2) }}</p>
        </div>

        <div class="bg-white p-5 rounded-2xl shadow text-center">
          <p class="text-gray-500 text-sm">Total Expenses</p>
          <p class="text-2xl font-semibold text-red-600 mt-1">-${{ totalExpenses.toFixed(2) }}</p>
        </div>

        <div class="bg-white p-5 rounded-2xl shadow text-center">
          <p class="text-gray-500 text-sm">Budget Status</p>
          <p class="font-semibold mt-1" :class="{ 'text-red-500': isOverBudget }">
            {{ budgetStatus }}
          </p>
        </div>
      </div>

      <!-- Budget Progress Bar -->
      <div class="bg-white p-6 rounded-2xl shadow mb-8">
        <div class="flex justify-between text-sm mb-2">
          <span class="font-medium">Budget Used</span>
          <span>{{ expensePercentage }}%</span>
        </div>
        <div class="h-4 bg-gray-200 rounded-full overflow-hidden">
          <div
            class="h-full bg-red-500 transition-all duration-300"
            :style="{ width: expensePercentage + '%' }"
          ></div>
        </div>
        <p class="text-xs text-gray-500 mt-2">
          Budget Limit: <span class="font-medium">${{ budgetLimit }}</span>
        </p>

        <!-- Add this inside the Budget Progress section -->
        <div class="mt-4 flex items-center gap-3">
          <label class="text-sm font-medium text-gray-600">Budget Limit:</label>
          <input
            v-model="budgetLimit"
            type="number"
            class="border border-gray-300 px-3 py-1 rounded-lg w-28 focus:outline-none focus:border-blue-500"
          />
          <button @click="budgetLimit = 1000" class="text-xs text-blue-600 hover:underline">
            Reset to 1000
          </button>
        </div>
      </div>

      <!-- Filters -->
      <div class="flex gap-2 mb-6">
        <button
          @click="filterType = 'all'"
          :class="{ 'bg-blue-600 text-white': filterType === 'all' }"
          class="px-6 py-2 rounded-xl border transition-colors"
        >
          All
        </button>
        <button
          @click="filterType = 'income'"
          :class="{ 'bg-blue-600 text-white': filterType === 'income' }"
          class="px-6 py-2 rounded-xl border transition-colors"
        >
          Income
        </button>
        <button
          @click="filterType = 'expense'"
          :class="{ 'bg-blue-600 text-white': filterType === 'expense' }"
          class="px-6 py-2 rounded-xl border transition-colors"
        >
          Expense
        </button>
      </div>

      <!-- Transaction List -->
      <div class="bg-white rounded-2xl shadow overflow-hidden">
        <div
          v-for="t in filteredTransactions"
          :key="t.id"
          class="flex justify-between items-center p-5 border-b last:border-none hover:bg-gray-50"
        >
          <div>
            <p class="font-medium">{{ t.desc }}</p>
            <p class="text-xs text-gray-500">{{ t.date }}</p>
          </div>

          <div class="flex items-center gap-6">
            <span
              class="font-semibold text-lg"
              :class="{
                'text-green-600': t.type === 'income',
                'text-red-600': t.type === 'expense',
              }"
            >
              {{ t.type === 'income' ? '+' : '-' }}${{ t.amount }}
            </span>

            <button
              @click="deleteTransaction(t.id)"
              class="text-red-500 hover:text-red-700 font-medium text-sm"
            >
              Delete
            </button>
          </div>
        </div>

        <div v-if="filteredTransactions.length === 0" class="p-12 text-center text-gray-400">
          No transactions yet. Add one above!
        </div>
      </div>

      <!-- Clear All Button -->
      <div class="text-center mt-8">
        <button @click="clearAll" class="text-red-500 hover:text-red-700 text-sm font-medium">
          Clear All Transactions
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'

const transactions = ref([]) //store transations
const filterType = ref('all') //'all', 'income' or 'expenses'
const budgetLimit = ref(1000)

const newDesc = ref('')
const newAmount = ref('')
const newType = ref('expense')

//---------------computed---------------------------
// Filtered Transactions
const filteredTransactions = computed(() => {
  if (filterType.value === 'all') {
    return transactions.value
  }

  return transactions.value.filter((t) => t.type === filterType.value)
})

// Total Income
const totalIncome = computed(() => {
  return transactions.value
    .filter((t) => t.type === 'income')
    .reduce((sum, t) => sum + Number(t.amount), 0)
})

// Total Expenses
const totalExpenses = computed(() => {
  return transactions.value
    .filter((t) => t.type === 'expense')
    .reduce((sum, t) => sum + Number(t.amount), 0)
})

//Balance
const balance = computed(() => {
  return totalIncome.value - totalExpenses.value
})

// Is over badget?
const isOverBudget = computed(() => {
  return totalExpenses.value > budgetLimit.value
})

const expensePercentage = computed(() => {
  if (totalIncome.value === 0) return 0
  const percent = (totalExpenses.value / totalIncome.value) * 100
  return Math.min(Math.round(percent), 100)
})

// Budget Status Label
const budgetStatus = computed(() => {
  if (isOverBudget.value) return 'Over Budget!!!'
  if (totalExpenses.value > budgetLimit.value) return 'Near limit!!!'
  return 'Good'
})

//-----------------Method------------------
const addTransaction = () => {
  // Prevent adding empty transations
  if (!newDesc.value.trim() || !newAmount.value) {
    alert('Please enter description and amount')
    return
  }

  // Create new Transaction object
  const newTransaction = {
    id: Date.now(),
    desc: newDesc.value.trim(),
    amount: Number(newAmount.value),
    type: newType.value,
    date: new Date().toISOString().split('T')[0], //Today's date
  }

  // add to the list
  transactions.value.push(newTransaction)

  //Clear the form
  newDesc.value = ''
  newAmount.value = ''
}

const deleteTransaction = (id) => {
  // Remove the transaction with matching id
  transactions.value = transactions.value.filter((t) => t.id !== id)
}

const clearAll = () => {
  if (confirm('Are you sure you want to delete ALL transactions?')) {
    transactions.value = []
  }
}

// Add this method
const resetBudget = () => {
  budgetLimit.value = 1000
}

//----------watchers----------------
// auto-save transaction
watch(
  transactions,
  (newTransaction) => {
    localStorage.setItem('finance-transaction', JSON.stringify(newTransaction))
  },
  { deep: true },
) //deep: true is important for arrays/objects

onMounted(() => {
  const saved = localStorage.getItem('finanace-transactions')
  if (saved) {
    transactions.value = JSON.parse(saved)
  }
})

watch(balance, (newBalance) => {
  if (newBalance < 0) {
    console.warn('You are in negative balance!')
  }
})

watch(isOverBudget, (over) => {
  if (over) {
    console.log('Budget exceeded!')
  }
})
</script>
