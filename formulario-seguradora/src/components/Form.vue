<template>
  <div>
    <label>Tipo da solicitação:</label>
    <select v-model="selectedSolicitationType">
      <option v-for="type in solicitationTypes" :key="type.slug" :value="type.slug">{{ type.name }}</option>
    </select>
    
    <div v-if="selectedSolicitationType === 'judicial'">
      <FormularioJudicial />
    </div>
    
    <div v-else-if="selectedSolicitationType === 'contractual'">
      <FormularioContratual />
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import FormularioJudicial from './FormularioJudicial.vue';
import FormularioContratual from './FormularioContratual.vue';

export default {
  components: {
    FormularioJudicial,
    FormularioContratual
  },
  data() {
    return {
      selectedSolicitationType: null,
      solicitationTypes: [],
    };
  },
  async created() {
    try {
      const response = await axios.get('https://6fe91ca9-7e4d-4648-91e3-b0274a86dc58.mock.pstmn.io/api/get-solicitation-types');
      this.solicitationTypes = response.data.data;
    } catch (error) {
      console.error('Error fetching solicitation types:', error);
    }
  },
  methods: {

  }
};
</script>


<style>
  select {
    display: block;
    padding: 10px 6px;
    width: 100%;
    box-sizing: border-box;
    border: none;
    border-bottom: 1px solid #ddd;
    color: #555;
  }
</style>