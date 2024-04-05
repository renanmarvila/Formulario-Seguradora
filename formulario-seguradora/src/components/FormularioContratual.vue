<template>
  <div>
    <p><label>Nome completo:</label>
    <input v-model="state.segurado.name" placeholder="">
    <span v-if="v$.segurado.name.$error">O campo é obrigatório e deve conter NOME e SOBRENOME</span></p>
    <p><label>Documento válido (CPF):</label>
    <input v-model="state.segurado.document" placeholder="">
    <span v-if="v$.segurado.document.$error">Digite um CPF válido</span></p>
    <p><label>CEP:</label>
    <input v-model="state.segurado.cep" @blur="handleCep" placeholder="">
    <span v-if="v$.segurado.cep.$error">O campo é obrigatório e deve conter mais de 2 caractéres</span></p>
    <p><label>Rua:</label>
    <input v-model="state.segurado.street" placeholder="">
    <span v-if="v$.segurado.street.$error">O campo é obrigatório e deve conter mais de 2 caractéres</span></p>
    <p><label>Bairro:</label>
    <input v-model="state.segurado.neighborhood" placeholder="">
    <span v-if="v$.segurado.neighborhood.$error">O campo é obrigatório e deve conter mais de 2 caractéres</span></p>
    
    <p><label>Valor do processo (R$):</label>
    <input v-model="state.processo.amount" placeholder=""> 
    <span v-if="v$.processo.amount.$error">O valor do processo deve ser maior que R$0,01 e menor que R$52482,55</span></p> 
    
    <label>Observações:</label>
    <textarea v-model="state.processo.observations" placeholder="Insira qualquer informação importante relacionada a solicitação"></textarea>
    <button @click="handleSubmit">Enviar</button>
  </div>
</template>
  
<script>

import { required, minLength, minValue, maxValue } from '@vuelidate/validators'
import useVuelidate from '@vuelidate/core'
import { reactive, computed } from 'vue'

// Função para validar CPF - Testado

function isValidCPF(value) {
  if (!value) return true; // Se o valor estiver vazio, não há necessidade de validação
  
  value = value.replace(/[^\d]+/g,''); // Remove caracteres não numéricos

  if (value.length !== 11 || /^(.)\1+$/.test(value)) return false; // Verifica se o CPF tem 11 dígitos e não é uma sequência de números repetidos

  let sum = 0;
  let rest;

  for (let i = 1; i <= 9; i++) sum = sum + parseInt(value.substring(i - 1, i)) * (11 - i);
  rest = (sum * 10) % 11;

  if ((rest === 10) || (rest === 11)) rest = 0;
  if (rest !== parseInt(value.substring(9, 10))) return false;

  sum = 0;
  for (let i = 1; i <= 10; i++) sum = sum + parseInt(value.substring(i - 1, i)) * (12 - i);
  rest = (sum * 10) % 11;

  if ((rest === 10) || (rest === 11)) rest = 0;
  if (rest !== parseInt(value.substring(10, 11))) return false;

  return true;
}

// Função para garantir que haja nome e sobrenome

function hasLastName(value) {
  if (!value) return false;
  const names = value.split(' ');
  if (names.length < 2) return false; // Pelo menos um nome e um sobrenome
  const lastName = names.pop(); // Último nome
  return lastName.length >= 1; // Sobrenome deve ter pelo menos 1 caractere
}

export default {
  setup () {
    const state = reactive({
      segurado: {
        name: '',
        document: '',
        cep: '',
        street: '',
        neighborhood: ''
      },
      processo: {
        amount: '',
        observations: '',
      },
    })

    const rules = computed(() => {
      return{
        segurado: {
          name: { required, minLength: minLength(2), hasLastName },
          document: { required, isValidCPF },
          cep: { required },
          street: { required, minLength: minLength(2) },
          neighborhood: { required, minLength: minLength(2) }
                  },
        processo: {
          amount: { required, minValue: minValue(0.01), maxValue: maxValue(52482.55) }
                  }
            }
    })

    const v$ = useVuelidate(rules, state)

    return {
      state,
      v$,
    }
  },

  methods: {
    handleSubmit() {
      this.v$.$validate()
      if (this.v$.$error) {
        alert('Há erros de validação no formulário. Por favor, corrija-os antes de enviar.');
        return;
      } else {
        // Caso não tenha erros, o formulário será salvo e o cliente loggado
        const logObject = {
           solicitation_type: "contractual",
            insured: {
              name: this.state.segurado.name,
              document: this.state.segurado.document,
              cep: this.state.segurado.cep,
              street: this.state.segurado.street,
              neighborhood: this.state.segurado.neighborhood
            },
            contract: {
            insured_amount: parseFloat(this.state.processo.amount),
            observations: this.state.processo.observations
        }
        }
        alert('Formulário enviado com sucesso')

        console.log(logObject); // Logga o objeto criado
      }
    },

    async handleCep() {
      try{
          const response = await fetch(`https://viacep.com.br/ws/${this.state.segurado.cep}/json/`)
          const data = await response.json()

          this.state.segurado.street = data.logradouro
          this.state.segurado.neighborhood = data.bairro
      } catch(error){
        console.log(error)
      }
    }
  }
}
</script>
  
<style>
  label {
    color: #aaa;
    display: inline-block;
    margin: 25px 0 15px;
    font-size: 0.7em;
    text-transform: uppercase;
    letter-spacing: 1px;
    font-weight: bold;
  }

  input, textarea {
    display: block;
    padding: 10px 6px;
    width: 100%;
    box-sizing: border-box;
    border: none;
    border-bottom: 1px solid #ddd;
    color: #555;
  }

  p {
    color: red;
  }
  
</style>