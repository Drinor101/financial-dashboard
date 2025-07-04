<template>
  <div class="min-h-screen bg-gray-100 p-6">
    <EstimatesTable />
    <CashFlowChart />
  </div>
</template>

<script setup>
import EstimatesTable from './components/EstimatesTable.vue';
import CashFlowChart from './components/CashFlowChart.vue';
</script>