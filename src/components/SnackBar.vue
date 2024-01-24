<template>
  <div v-if="isVisible" :class="[ 'snackbar', getColorClass() ]">
    {{ message }}
  </div>
</template>

<script>
export default {
  props: {
    isVisible: Boolean,
    message: String,
    type: String,
  },
  methods: {
    getColorClass() {
      const colorMap = {
        error: 'bg-red-500',
        success: 'bg-green-700',
        warning: 'bg-yellow-500',
        // Adicione mais tipos e cores conforme necessário
      };

      // Use a classe de cor do Tailwind com base no tipo
      return this.type && colorMap[this.type] ? colorMap[this.type] : 'bg-gray-500';
    },
  },
};
</script>

<style scoped>
.snackbar {
  position: fixed;
  top: 16px;
  left: 50%;
  transform: translateX(-50%);
  color: #fff;
  padding: 16px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
</style>
