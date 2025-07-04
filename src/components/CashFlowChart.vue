<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-50 via-blue-50 to-indigo-100 p-4 sm:p-6 lg:p-8">
    <div class="max-w-4xl mx-auto">
      <!-- Header -->
      <div class="mb-8">
        <h2 class="text-3xl sm:text-4xl font-bold text-gray-900 mb-2">Cash Flow Projection</h2>
        <p class="text-gray-600 text-lg">Visualize your monthly inflows, outflows, and net cash flow</p>
      </div>

      <!-- Stats Row -->
      <div class="grid grid-cols-1 sm:grid-cols-3 gap-6 mb-8">
        <div class="bg-white rounded-2xl p-5 shadow-md border border-gray-100 flex flex-col items-center">
          <span class="text-sm text-gray-500 mb-1">Total Inflow</span>
          <span class="text-2xl font-bold text-green-600">€{{ totalInflow }}</span>
        </div>
        <div class="bg-white rounded-2xl p-5 shadow-md border border-gray-100 flex flex-col items-center">
          <span class="text-sm text-gray-500 mb-1">Total Outflow</span>
          <span class="text-2xl font-bold text-red-500">€{{ totalOutflow }}</span>
        </div>
        <div class="bg-white rounded-2xl p-5 shadow-md border border-gray-100 flex flex-col items-center">
          <span class="text-sm text-gray-500 mb-1">Net Flow</span>
          <span :class="'text-2xl font-bold ' + (totalNetFlowNumber >= 0 ? 'text-blue-600' : 'text-red-600')">€{{ totalNetFlow }}</span>
        </div>
      </div>

      <!-- Chart Card -->
      <div class="bg-white rounded-3xl shadow-xl border border-gray-100 p-6">
        <canvas id="cashFlowChart"></canvas>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref, computed } from 'vue';
import Chart from 'chart.js/auto';

const cashFlowData = ref({
  labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
  inflows: [5000, 7000, 8000, 6000, 9000, 10000, 11000, 9500, 12000, 13000, 12500, 14000],
  outflows: [3000, 4000, 5000, 4500, 6000, 7000, 7500, 6500, 8000, 8500, 9000, 9500],
});

const netFlow = computed(() =>
  cashFlowData.value.inflows.map((inflow, i) => inflow - cashFlowData.value.outflows[i])
);

const totalInflow = computed(() => cashFlowData.value.inflows.reduce((a, b) => a + b, 0).toLocaleString());
const totalOutflow = computed(() => cashFlowData.value.outflows.reduce((a, b) => a + b, 0).toLocaleString());
const totalNetFlowNumber = computed(() => cashFlowData.value.inflows.reduce((a, b) => a + b, 0) - cashFlowData.value.outflows.reduce((a, b) => a + b, 0));
const totalNetFlow = computed(() => totalNetFlowNumber.value.toLocaleString());

onMounted(() => {
  const ctx = document.getElementById('cashFlowChart') as HTMLCanvasElement;
  new Chart(ctx, {
    type: 'line',
    data: {
      labels: cashFlowData.value.labels,
      datasets: [
        {
          label: 'Inflows',
          data: cashFlowData.value.inflows,
          borderColor: 'rgba(34, 197, 94, 1)', // Tailwind green-500
          backgroundColor: 'rgba(34, 197, 94, 0.2)',
          fill: true,
          tension: 0.4,
          pointRadius: 4,
          pointHoverRadius: 6,
        },
        {
          label: 'Outflows',
          data: cashFlowData.value.outflows,
          borderColor: 'rgba(239, 68, 68, 1)', // Tailwind red-500
          backgroundColor: 'rgba(239, 68, 68, 0.2)',
          fill: true,
          tension: 0.4,
          pointRadius: 4,
          pointHoverRadius: 6,
        },
        {
          label: 'Net Flow',
          data: netFlow.value,
          borderColor: 'rgba(59, 130, 246, 1)', // Tailwind blue-500
          backgroundColor: 'rgba(59, 130, 246, 0.1)',
          fill: false,
          tension: 0.4,
          borderDash: [6, 4],
          pointRadius: 5,
          pointHoverRadius: 7,
        },
      ],
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      aspectRatio: 2,
      interaction: {
        mode: 'nearest',
        intersect: false,
      },
      plugins: {
        legend: {
          position: 'top',
          labels: {
            font: {
              size: 14,
            },
            padding: 20,
          },
        },
        tooltip: {
          enabled: true,
          callbacks: {
            label: (context) => {
              const value = context.parsed.y ?? context.parsed;
              return `${context.dataset.label}: €${value.toLocaleString()}`;
            },
          },
          padding: 8,
          bodyFont: { size: 14 },
        },
      },
      scales: {
        y: {
          beginAtZero: true,
          ticks: {
            font: { size: 13 },
            callback: (value) => `€${value.toLocaleString()}`,
            stepSize: 2000,
          },
          grid: {
            color: 'rgba(203, 213, 225, 0.3)',
            // borderDash: [5, 5], // Removed to fix linter error
          },
          title: {
            display: true,
            text: 'Amount (€)',
            font: { size: 14, weight: 'bold' },
            color: '#374151', // Tailwind gray-700
          },
        },
        x: {
          ticks: {
            font: { size: 13 },
            color: '#4B5563', // Tailwind gray-600
          },
          grid: {
            display: false,
          },
          title: {
            display: true,
            text: 'Months',
            font: { size: 14, weight: 'bold' },
            color: '#374151',
          },
        },
      },
    },
  });
});
</script>

<style scoped>
div > canvas {
  width: 100% !important;
  height: 300px !important;
}
</style>
  