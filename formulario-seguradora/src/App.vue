<script setup>
import Form from './components/Form.vue'
</script>

<template>
  <div>
    <a href="https://www.segdev.com.br/" target="_blank">
      <img src="./assets/Segdev.png" class="logo" alt="Segdev Logo" />
    </a>
  </div>
  <div class="container">
    <Form />
  </div>
</template>

<style scoped>
.logo {
  height: 15em;
  padding: 0.5em;
}

.background {
  background-color: rgba(102, 0, 238, 255);
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
    margin: 30px auto;
    background: white;
    text-align: left;
    padding: 40px;
    border-radius: 10px;
    width: 480px;
}


@media screen and (max-width: 768px) {
  .container {
    padding: 10px;
  }
}

@media screen and (max-width: 480px) {
  .container {
    border-radius: 5px;
  }
}
</style>
