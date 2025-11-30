<template>
  <div class="home-page">
    <div class="container">
      <header class="page-header">
        <h1 class="page-title">Сетевой калькулятор</h1>
        <p class="page-description">
          Калькулятор для расчета параметров IP-сетей и подсетей
        </p>
      </header>
      
      <main class="page-content">
        <SubnetCalculator />
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
import SubnetCalculator from '../components/SubnetCalculator.vue'
</script>

<style scoped>
.home-page {
  min-height: 100vh;
  background: linear-gradient(135deg, #f8fafc 0%, #e2e8f0 100%);
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
}

.page-header {
  text-align: center;
  margin-bottom: 3rem;
}

.page-title {
  color: #2563eb;
  font-size: 2.5rem;
  margin-bottom: 1rem;
  font-weight: 700;
}

.page-description {
  color: #64748b;
  font-size: 1.125rem;
  max-width: 600px;
  margin: 0 auto;
}

.page-content {
  display: flex;
  justify-content: center;
}
</style>