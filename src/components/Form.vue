<template>
  <Message :msg="msg" v-show="msg" />
  <div>
    <form id="form" method="POST" @submit="create">
      <div class="input-container">
        <label for="nome">Nome:</label>
        <input type="text" id="nome" name="nome" v-model="nome" placeholder="Digite o seu nome">
      </div>
      <div class="input-container">
        <label for="din">Escolha a quantia de dinheiro:</label>
        <select name="din" id="din" v-model="din">
          <option value="">Selecione a quantia</option>
          <option v-for="din in money" :key="din.id" :value="din.tipo">{{ din.tipo }}</option>
        </select>
      </div>
      <div class="input-container">
        <label for="vezes">Escolha a quantidade de parcela:</label>
        <select name="vezes" id="vezes" v-model="vezes">
          <option value="">Selecione a parcela</option>
          <option v-for="vezes in parc" :key="vezes.id" :value="vezes.tipo">{{ vezes.tipo }}</option>
        </select>
      </div>
      <div id="opcionais-container" class="input-container">
        <label id="opcionais-title" for="opcionais">Opcionais:</label>
        <div class="checkbox-container" v-for="opcional in opcionaisdata" :key="opcional.id">
          <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
          <span>{{ opcional.tipo }}</span>
        </div>
      </div>
      <div class="input-container">
        <input class="submit-btn" type="submit" value="Finalizar">
      </div>
    </form>
  </div>
</template>

<script>
import Message from './Message'

export default {
  name: "Form",
  data() {
    return {
      money: null,
      parc: null,
      opcionaisdata: null,
      nome: null,
      din: null,
      vezes: null,
      opcionais: [],
      status: "Solicitado",
      msg: null
    }
  },
  methods: {
    async getopcoes() {
      const req = await fetch('http://localhost:3000/opcoes')
      const data = await req.json()

      this.money = data.money
      this.parc = data.parc
      this.opcionaisdata = data.opcionais
    },
    async create(e) {

      e.preventDefault()

      const data = {
        nome: this.nome,
        vezes: this.vezes,
        din: this.din,
        opcionais: Array.from(this.opcionais),
        status: "Solicitado"
      }

      const dataJson = JSON.stringify(data)    

      const req = await fetch("http://localhost:3000/pedidos", {
        method: "POST",
        headers: { "Content-Type" : "application/json" },
        body: dataJson
      });

      const res = await req.json()

      console.log(res)

      this.msg = "Pedido realizado com sucesso!"

      // limpar mensagem
      setTimeout(() => this.msg = "", 3000)

      // limpar campos
      this.nome = ""
      this.vezes = ""
      this.din = ""
      this.opcionais = []
      
    }
  },
  mounted () {
    this.getopcoes()
  },
  components: {
    Message
  }
}
</script>

<style scoped>
  #form {
    max-width: 400px;
    margin: 0 auto;
  }

  .input-container {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
  }

  label {
    font-weight: bold;
    margin-bottom: 15px;
    color: #222;;
    padding: 5px 10px;
    border-left: 4px solid #228b22;
  }

  input, select {
    padding: 5px 10px;
    width: 300px;
  }

  #opcionais-container {
    flex-direction: row;
    flex-wrap: wrap;
  }

  #opcionais-title {
    width: 100%;
  }

  .checkbox-container {
    display: flex;
    align-items: flex-start;
    width: 50%;
    margin-bottom: 20px;
  }

  .checkbox-container span,
  .checkbox-container input {
    width: auto;
  }

  .checkbox-container span {
    margin-left: 6px;
    font-weight: bold;
  }

  .submit-btn {
    background-color: #222;
    color:#228b22;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }

  .submit-btn:hover {
    background-color: transparent;
    color: #222;
  }
</style>