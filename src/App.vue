<template>
  <div>
    <Loading :is-loading="loading"></Loading>
    <Container>
      <div>
        <SnackBar :isVisible="snackBar.showSnackBar" :message="snackBar.snackBarMessage" :type="snackBar.snackBarType" />
      </div>
      <TopBar @upload-success="fetchData"/>


      <Box v-if="info">
          <div class="flex flex-col items-center max-w-1280 w-1280">
          <div class="hint-container mb-6">
            <p class="hint-text">Dica:</p>
            <p class="hint-description">Clique em "Upload" para carregar um arquivo .xlsx ou .csv. Após, 
              visualize os gráficos.
            </p>
          </div>
        </div>
      </Box>

      <Box v-if="timeOut">
          <div class="flex flex-col items-center max-w-1280 w-1280">
          <div class="hint-container mb-6">
            <p class="hint-text">Atenção:</p>
            <p class="hint-description">Desculpe, a requisição ultrapassou o tempo limite, 
              tente novamente mais tarde!
            </p>
          </div>
        </div>
      </Box>


      <div class="scrollable-container">
        <div v-for="yearData in uniqueYears" :key="yearData.year">
          <div class="w-full" style="justify-content: center; display: flex;">
            <span class="card-title">{{ yearData.year }}</span>
          </div>
          <Box>
            <Card :items="yearData.items" />
          </Box>
        </div>
      </div>

    </Container>
  </div>
</template>

<script>
import axios from 'axios';
import { apiClient } from './apiService';
import Box from './components/Box.vue';
import Card from './components/Card.vue';
import Container from './components/Container.vue';
import Loading from './components/Loading.vue';
import SnackBar from './components/SnackBar.vue';
import TopBar from './components/TopBar.vue';

export default {
  components: {
    Container,
    TopBar,
    Box,
    Card,
    SnackBar,
    Loading
},

  data() {
    return {
      data: [],
      snackBar : {
        showSnackBar: false,
        snackBarMessage: '',
        snackBarType: '',
      },
      loading: false,
      info: false,
      timeOut: false
    };
  },
  computed: {
    uniqueYears() {
      const uniqueYearsSet = new Set();
      const uniqueYearsArray = [];

      this.data.forEach(item => {
        if (!uniqueYearsSet.has(item.year)) {
          uniqueYearsSet.add(item.year);

          uniqueYearsArray.push({
            year: item.year,
            items: [],
          });
        }

        const yearIndex = uniqueYearsArray.findIndex(yearData => yearData.year === item.year);
        uniqueYearsArray[yearIndex].items.push({
          month: item.month,
          labels: item.labels,
          data: item.data,
        });
      });

      return uniqueYearsArray;
    },
  },
  methods: {
    async fetchData() {
      this.loading = true;
      this.info = false;
      try {
        const response = await apiClient.get('/plan');
        const responseData = response.data;

        const filteredAndSortedData = responseData
          .filter(item => item.year) 
          .sort((a, b) => a.year - b.year);

          if(responseData.length === 0) this.info = true;

        this.data = filteredAndSortedData;

        this.loading = false;
        
      } catch (error) {
        this.loading = false;
        if (axios.isAxiosError(error)) {
          this.timeOut = true;
          console.error('Erro de requisição:', error.message);
        } else {
          console.error('Erro ao processar a requisição:', error.message);
        }
      }
    },

    openSnackBar(message) {
      this.snackBar ={
        snackBarMessage: message,
        showSnackBar: true,
        snackBarType: 'success',
      } 
      setTimeout(() => this.closeSnackBar(), 4000);
    },
    closeSnackBar() {
      this.snackBar ={
        snackBarMessage: '',
        showSnackBar: false,
        snackBarType: '',
      } 
    },
  },
  mounted() {
    this.fetchData();
  },
};
</script>

<style scoped>
.hint-container {
  background-color: #f0f0f0;
  padding: 10px;
  border-radius: 8px;
  margin-bottom: 20px;
  max-width: 420px;
  max-height: 100px;
  margin-top: 30px;
}

.hint-text {
  font-weight: bold;
  margin: 0;
}

.card-title {
  font-size: 1.2rem;
  padding: 1.2rem;
  font-weight: 700;
  margin-bottom: 10px;
}

.scrollable-container {
  max-height: 90vh;
  overflow-y: auto;
}
.scrollable-container::-webkit-scrollbar {
  width: 2px;
}

.scrollable-container::-webkit-scrollbar-thumb {
  background-color: #888; 
  border-radius: 4px; 
}

.scrollable-container::-ms-scrollbar {
  width: 8px;
}

.scrollable-container::-ms-scrollbar-thumb {
  background-color: #888;
  border-radius: 4px;
}

.bg-gray-200 {
    --tw-bg-opacity: 1;
    background-color: red;
}
</style>
