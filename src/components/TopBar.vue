<template>
  <div>
    <SnackBar :isVisible="snackBar.showSnackBar" :message="snackBar.snackBarMessage" :type="snackBar.snackBarType" />
  </div>
  <div class="w-full" style="background-color: #41167f;">
    <div class="mx-auto flex items-center justify-between pt-3 pb-3 sm:px-2 md:px-2 lg:px-2 xl:px-0 max-w-1280 padding_mobile">
      <button>
        <img src="../assets/logo.svg" alt="Descrição da imagem" />
      </button>
      <label for="upload" class="text-white p-2 rounded flex items-center bg-teal-700 cursor-pointer">
        <h6 class="pr-2">Upload</h6>
        <img src="../assets/upload_icon.svg" alt="Ícone de Upload" class="w-4 h-4" />
        <input type="file" id="upload" class="hidden" @change="handleUpload" />
      </label>
    </div>
  </div>
</template>

<script>

import { apiClient } from '../apiService';
import SnackBar from './SnackBar.vue';

export default {
  components: {
    SnackBar,
  },
  props: {
    fetchData: Function,
  },
  emits: ['upload-success'],
  data() {
    return {
      data: [],
      snackBar : {
        showSnackBar: false,
        snackBarMessage: '',
        snackBarType: 'error',
      },
    };
  },
  name: 'TopBar',
  methods: {
    async handleUpload(event) {
      const file = event.target.files[0];

      if (file) {
        try {
          const response = await apiClient.post('/upload', { file }, {
            headers: { 
              'Content-Type': 'multipart/form-data',
            },
          });

          if (response.status === 201) {
            this.openSnackBar("Arquivo carregado com sucesso!", 'success');
            this.$emit('upload-success');
          } 
        } catch (error) {
          this.openSnackBar(error.response.data.message, 'error');
          console.error('Erro ao fazer upload:', error);
        }
      } else {
        console.warn("Nenhum arquivo selecionado.");
      }
    },

    openSnackBar(message, type) {
      this.snackBar ={
        snackBarMessage: message,
        showSnackBar: true,
        snackBarType: type,
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
  }
}
</script>

<style scoped>
@media (max-width: 639px) {
  .padding_mobile {
    padding-left: 8px;
    padding-right: 8px;
  }
}
</style>
