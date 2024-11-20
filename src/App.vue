<template>

<Header></Header>
<div class="container">
  <Balance :total="+total"></Balance>
  <IncomeExpense :income="+income" :expenses="+expenses"></IncomeExpense>
  <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"></TransactionList>
  <AddTransaction @transactionSubmitted="handleTransactionSubmitted"></AddTransaction>
</div>
</template>

<script setup>
  import Header from './components/Header.vue';
  import Balance from './components/Balance.vue';
  import IncomeExpense from './components/IncomeExpense.vue';
  import TransactionList from './components/TransactionList.vue';
  import AddTransaction from './components/AddTransaction.vue';
  import {useToast} from 'vue-toastification';
  import {ref, computed, onMounted} from 'vue';
  const toast = useToast();
  const transactions = ref([
          
    ]);
    onMounted(()=>{
      const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
      if(savedTransactions){
        transactions.value = savedTransactions;
      }
    });
    const total = computed(()=>{
      return transactions.value.reduce((acc, transaction)=>{
        return acc + transaction.amount;
      },0)
    }); 

    const income = computed(()=>{
      return transactions.value.filter((transaction)=>transaction.amount>0)
      .reduce((acc, transaction)=>{
        return acc + transaction.amount;
      },0).toFixed(2);
    }); 

    const expenses = computed(()=>{
      return transactions.value.filter((transaction)=>transaction.amount<0)
      .reduce((acc, transaction)=>{
        return acc + transaction.amount;
      },0).toFixed(2);
    }); 
//add transaction
    const handleTransactionSubmitted = (transactionData) =>{

      transactions.value.push({
        id:generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount
      });
      saveTransactionsToLocalStorage();
      toast.success('Transaction added')
    };

    const generateUniqueId = ()=>{
      return Math.floor(Math.random()*1000000);
    }
    //delete tran
    const handleTransactionDeleted = (id)=>{
      transactions.value = transactions.value.filter((transaction)=>transaction.id!==id);
      saveTransactionsToLocalStorage();
      toast.success("Transaction deleted")
    }

    //save to localStorage
    const saveTransactionsToLocalStorage=()=>{
      localStorage.setItem('transactions', JSON.stringify(transactions.value));
    }
</script>