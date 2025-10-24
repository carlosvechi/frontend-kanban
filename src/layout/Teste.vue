<template>
  <div class="container">
    <h1>{{ mensagem }}</h1>
    <button @click="carregarMensagem">Testar API Laravel</button>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      mensagem: 'Clique no botão para testar a API',
    }
  },
  methods: {
    async carregarMensagem() {
      this.mensagem = 'Carregando...'
      try {
        const resposta = await axios.get('http://127.0.0.1:8000/api/v1/teste')
        console.log('Resposta da API:', resposta.data)
        // 👇 aqui a gente testa se existe "mensagem" no retorno
        if (resposta.data.mensagem) {
          this.mensagem = resposta.data.mensagem
        } else {
          this.mensagem = JSON.stringify(resposta.data)
        }
      } catch (erro) {
        console.error('Erro ao conectar com a API:', erro)
        this.mensagem = 'Erro ao conectar com a API.'
      }
    },
  },
}
</script>
