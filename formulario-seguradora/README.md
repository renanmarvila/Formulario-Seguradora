


# Formulário de Seguradora

Projeto realizado para prática de consumo de API em criação de forms, validação de campos de formulário, logging, etc.

# Validações obrigatórias

- [x]  CPF e CNPJ Válidos - Pra isso são utilizadas funções que verificam se a combinação digitada é um possível CPF ou CNPJ 
- [x]  Nome completo (com sobrenome) - É utilizada uma função para verificar se o número de palavras nesse campo é maior ou igual a 2, fazendo com que seja obrigatório o uso de nome e sobrenome, nem que seja abreviado. (Ex.: "Renan M" e "R M" são válidos, enquanto "Renan" ou "Renan " não são válidos )
- [x]  Rua e bairro com 2 ou mais caractéres - Caso a API não consiga completar os campos automaticamente com o valor de CEP preenchido pelo cliente
- [x]  Valor máximo do processo de acordo com o tipo de solitação - Ao escolher o tipo de solicitação o valor máximo do processo é alterado

Todos os campos acimas são obrigatórios e o formulário só é enviado caso todos os campos passem pela validação. Foi utilizado o Vuelidate (https://vuelidate-next.netlify.app/) para validação dos campos.








## Autores

- [Github](https://www.github.com/renanmarvila)
- [LinkedIn](https://www.linkedin.com/in/renanmarvila)


