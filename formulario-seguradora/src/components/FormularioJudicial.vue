<template>
  <div>
    <p><label>Nome completo:</label>
    <input v-model="state.tomador.name" placeholder="">
    <span v-if="v$.tomador.name.$error">O campo é obrigatório e deve conter NOME e SOBRENOME</span></p>
    <p><label>Documento válido (CNPJ):</label>
    <input v-model="state.tomador.document" placeholder="">
    <span v-if="v$.tomador.document.$error">Digite um CNPJ válido</span></p>
    <p><label>CEP:</label>
    <input v-model="state.tomador.cep" @blur="handleCep" placeholder="">
    <span v-if="v$.tomador.cep.$error">O campo é obrigatório e deve conter mais de 2 caractéres</span></p>
    <p><label>Rua:</label>
    <input v-model="state.tomador.street" placeholder="">
    <span v-if="v$.tomador.street.$error">O campo é obrigatório e deve conter mais de 2 caractéres</span></p>
    <p><label>Bairro:</label>
    <input v-model="state.tomador.neighborhood" placeholder="">
    <span v-if="v$.tomador.neighborhood.$error">O campo é obrigatório e deve conter mais de 2 caractéres</span></p>
   
    <p><label>Valor do processo (R$):</label>
    <input v-model="state.processo.amount" placeholder="">
    <span v-if="v$.processo.amount.$error">O valor do processo deve ser maior que R$0,01 e menor que R$25269,30</span></p>
    
    <label>Observações:</label>
    <textarea v-model="state.processo.observations" placeholder="Insira qualquer informação importante relacionada a solicitação"></textarea>
    <button @click="handleSubmit">Enviar</button>
  </div>
</template>
  
<script>

import { required, minLength, minValue, maxValue, numeric } from '@vuelidate/validators'
import useVuelidate from '@vuelidate/core'
import { reactive, computed } from 'vue'

// Funções para validar campos que não são direto pelo Vuelidate

// Função para validar CNPJ - Testado 

function isValidCNPJ(cnpj) {
    var b = [ 6, 5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2 ]
    var c = String(cnpj).replace(/[^\d]/g, '')
    
    if(c.length !== 14)
        return false

    if(/0{14}/.test(c))
        return false

    for (var i = 0, n = 0; i < 12; n += c[i] * b[++i]);
    if(c[12] != (((n %= 11) < 2) ? 0 : 11 - n))
        return false

    for (var i = 0, n = 0; i <= 12; n += c[i] * b[i++]);
    if(c[13] != (((n %= 11) < 2) ? 0 : 11 - n))
        return false

    return true
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
      tomador: {
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
        tomador: {
          name: { required, minLength: minLength(2), hasLastName },
          document: { required, isValidCNPJ },
          cep: { required },
          street: { required, minLength: minLength(2) },
          neighborhood: { required, minLength: minLength(2) }
                  },
        processo: {
          amount: { required, minValue: minValue(0.01), maxValue: maxValue(25269.30) }
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
            solicitation_type: "judicial",
            policy_holder: {
              name: this.state.tomador.name,
              document: this.state.tomador.document,
              cep: this.state.tomador.cep,
              street: this.state.tomador.street,
              neighborhood: this.state.tomador.neighborhood
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
          const response = await fetch(`https://viacep.com.br/ws/${this.state.tomador.cep}/json/`)
          const data = await response.json()

          this.state.tomador.street = data.logradouro
          this.state.tomador.neighborhood = data.bairro
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

  button {
    margin-top: 20px;
  }
  
</style>