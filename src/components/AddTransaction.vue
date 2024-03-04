<template>
  <h3>Add new transaction</h3>
  <form id="form" @submit.prevent="onSubmit">
    <!-- Transaction Type Selector -->
    <div class="form-control">
      <label for="type">Type</label>
      <select id="type" v-model="type">
        <option value="income">Income</option>
        <option value="expense">Expense</option>
      </select>
    </div>
    
    <!-- Dynamic Dropdown based on Transaction Type -->
    <div class="form-control" v-if="type === 'income'">
      <label for="income-type">Income Type</label>
      <select id="income-type" v-model="text">
        <option v-for="income in potentialIncomes" :value="income">{{ income }}</option>
      </select>
    </div>
    <div class="form-control" v-else>
      <label for="expense-type">Expense Type</label>
      <select id="expense-type" v-model="text">
        <option v-for="expense in potentialExpenses" :value="expense">{{ expense }}</option>
      </select>
    </div>

    <!-- Free Text Option -->
    <div class="form-control" v-if="text === 'Other'">
    <label for="custom-text">Custom Description</label>
    <input type="text" id="custom-text" placeholder="Enter description..." v-model="customText" />
  </div>

    <!-- Amount Input -->
    <div class="form-control">
      <label for="amount">Amount</label>
      <input type="number" id="amount" placeholder="Enter amount..." v-model="amount" />
    </div>

    <button class="btn">Add transaction</button>
  </form>
</template>


<script setup>
import { useToast } from 'vue-toastification';
import { ref, defineProps, defineEmits } from 'vue';

const props = defineProps({
  potentialIncomes: Array,
  potentialExpenses: Array,
});

// Declare necessary refs
const type = ref('income');
const text = ref('');
const customText = ref('');
const amount = ref('');


// Get toast interface
const toast = useToast();

const emit = defineEmits(['transactionSubmitted']);

const onSubmit = () => {
  let transactionText = text.value === 'Other' ? customText.value : text.value;

  if (!transactionText || !amount.value) {
    toast.error('All fields must be filled.');
    return;
  }

  let transactionAmount = parseFloat(amount.value);
  // If the transaction is an expense and the amount is positive, convert it to a negative number
  if (type.value === 'expense' && transactionAmount > 0) {
    transactionAmount *= -1;
  }

  const transactionData = {
    text: transactionText,
    amount: transactionAmount,
  };

  emit('transactionSubmitted', transactionData);

  // Clear form fields
  text.value = '';
  customText.value = '';
  amount.value = '';
  type.value = 'income'; 
};

</script>