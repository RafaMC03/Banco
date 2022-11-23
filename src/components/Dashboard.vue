<template>
  <div id="solicitacao-table" v-if="pedidos">
    <div>
      <div id="solicitacao-table-heading">
        <div class="order-id">#:</div>
        <div>Cliente:</div>
        <div>Quantidade(R$):</div>
        <div>Parcelamento:</div>
        <div>Opcionais:</div>
        <div>Ações:</div>
      </div>
    </div>
    <div id="solicitacao-table-rows">
      <div class="solicitacao-table-row" v-for="cliente in pedidos" :key="cliente.id">
        <div class="order-number">{{ cliente.id }}</div>
        <div>{{ cliente.nome }}</div>
        <div>{{ cliente.din }}</div>
        <div>{{ cliente.vezes }}</div>
        <div>
          <ul v-for="(opcional, index) in cliente.opcionais" :key="index">
            <li>{{ opcional }}</li>
          </ul>
        </div>
        <div>
          <select name="status" class="status" @change="updatecliente($event, cliente.id)">
            <option :value="s.tipo" v-for="s in status" :key="s.id" :selected="cliente.status == s.tipo">
              {{ s.tipo }}
            </option>
          </select>
          <button class="delete-btn" @click="deletecliente(cliente.id)">Excluir</button>
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    <h2>Não há pedidos no momento!</h2>
  </div>
</template>
<script>
  export default {
    name: "Dashboard",
    data() {
      return {
        pedidos: null,
        cliente_id: null,
        status: []
      }
    },
    methods: {
      async getPedidos() {
        const req = await fetch('http://localhost:3000/pedidos')

        const data = await req.json()

        this.pedidos = data

        // Resgata os status de pedidos
        this.getStatus()

      },
      async getStatus() {

        const req = await fetch('http://localhost:3000/status')

        const data = await req.json()

        this.status = data

      },
      async deletecliente(id) {

        const req = await fetch(`http://localhost:3000/pedidos/${id}`, {
          method: "DELETE"
        });

        const res = await req.json()

        this.getPedidos()

      },
      async updatecliente(event, id) {

        const option = event.target.value;

        const dataJson = JSON.stringify({status: option});

        const req = await fetch(`http://localhost:3000/pedidos/${id}`, {
          method: "PATCH",
          headers: { "Content-Type" : "application/json" },
          body: dataJson
        });

        const res = await req.json()

        console.log(res)

      }
    },
    mounted () {
    this.getPedidos()
    }
  }
</script>

<style scoped>
  #solicitacao-table {
    max-width: 1200px;
    margin: 0 auto;
  }

  #solicitacao-table-heading,
  #solicitacao-table-rows,
  .solicitacao-table-row {
    display: flex;
    flex-wrap: wrap;
  }

  #solicitacao-table-heading {
    font-weight: bold;
    padding: 12px;
    border-bottom: 3px solid #333;
  }

  .solicitacao-table-row {
    width: 100%;
    padding: 12px;
    border-bottom: 1px solid #CCC;
  }

  #solicitacao-table-heading div,
  .solicitacao-table-row div {
    width: 19%;
  }

  #solicitacao-table-heading .order-id,
  .solicitacao-table-row .order-number {
    width: 5%;
  }

  select {
    padding: 12px 6px;
    margin-right: 12px;
  }

  .delete-btn {
    background-color: #222;
    color:	#228b22;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }
  
  .delete-btn:hover {
    background-color: transparent;
    color: #222;
  }
  
</style>