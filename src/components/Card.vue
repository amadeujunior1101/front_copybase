<template>
    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 w-1280 sm:px-2 md:px-2 lg:px-2 xl:px-0 padding_mobile">
      <div v-for="item in items" :key="item.month" class="p-4 bg-gray-200">
        <BarChart
            :labels="['MRR', 'Churn rate']"
            :data="item.data"
            :label="item.month"
            borderColor="rgba(75, 192, 192, 1)"
            :borderWidth="1"
        />
      </div>
    </div>
  </template>
  
  <script>
  import BarChart from './BarChart.vue';
  
  export default {
    name: 'Card',
    components: {
      BarChart
    },
    props: {
      items: {
        type: Array,
        required: true,
      },
    },
  }
  </script>
  
  <style>
  @media (max-width: 639px) {
    .padding_mobile {
      padding-left: 8px;
      padding-right: 8px;
    }
  }
  </style>
  