<template>
  <div id="burger-table">
     <Message :msg="msg" v-show="msg"/>
    <div>
      <div id="burger-table-heading">
        <div class="order-id">#:</div>
        <div>Cliente:</div>
        <div>Pão:</div>
        <div>Carne:</div>
        <div>Opcionais:</div>
        <div>Ações:</div>
      </div>
    </div>
    <div id="burger-table-rows">
      <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
        <div class="order-number">{{ burger.id }}</div>
        <div>{{ burger.nome }}</div>
        <div>{{ burger.pao }}</div>
        <div>{{ burger.carne }}</div>
        <div>
          <ul>
            <li v-for="(opcional, index) in burger.opcionais" :key="index">
              {{ opcional }}
            </li>
          </ul>
        </div>
        <div>
          <select name="status" class="status" @change="updatedBurger($event, burger.id)"> <!--Evento e change, quando houver uma mudança, dispara o evento de update.-->
            <option value="">Selecione</option>
            <!--Fazendo um vfor para se repetir na lista de options de acordo com os dados do banco na coluna de status-->
            <!--esse s que o cara colocou foi so pra nao ficar status in status....mas poderia.-->
            <!--para aparecer em todos os itens que foram solicitados, o status solicitado, é preciso fazer um if ternário.
          esse selected é nativo, ele acessa de forma dinamica o status do burger e verifica se é igual ao tipo-->
            <option
              v-for="s in status"
              :key="s.id"
              :value="s.tipo"
              :selected="burger.status == s.tipo" 
            > <!--if ternário acima -->
              {{ s.tipo }}
            </option>
          </select>
          <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Message from './Message.vue';

export default {
  name: "Dashboard",
  components:{
    Message
  },
  data() {
    return {
      burgers: null,
      burger_id: null,
      status: [],
       msg: null,
    };
  },
  methods: {
    async getPedidos() {
      const req = await fetch("http://localhost:3000/burgers");
      const data = await req.json();

      this.burgers = data;

      //resgatar os status
      this.getStatus();
    },
    async getStatus() {
      const req = await fetch("http://localhost:3000/status");
      const data = await req.json();
      this.status = data;
    },
    async deleteBurger(id){  // aqui esta sendo criado um metodo de deletar, baseado no id do burguer no banco de dados.

      const req = await fetch(`http://localhost:3000/burgers/${id}`, {  // essa contrução é espeficica do servidor q estamos usando, o json.serve.
        method: "DELETE" // aqui o vue vai entender que é deletar mesmo...hehe
      });// para usar o sifrao dessa forma, concatenando o id, tem q usar o sinal de crase. Dentro da propria chamada do 
      

      const res = await req.json();  // e aqui o res recebe o json . esse requisição sempre tem q acontecer, pega o json, depois tranforma em texto.

   //colocar ums msg de sistema.
    this.msg = `Pedido removido com sucesso!`; // linha para mostrar a msg quando for criado o item e mostrar o id.
   
   //limpa msg.
  setTimeout(() => this.msg = "", 3000 );


      this.getPedidos();  // isso aqui força uma atualizacao por meio do backend ...
    },
  
  async updatedBurger(event, id){
    
    const option = event.target.value ; // com esse codigo vamos no evento, pegando o valor, no caso do option, mas aqui ele nao ta fazendo nada ainda.
   const dataJson = JSON.stringify({ status: option});

   const req = await fetch(`http://localhost:3000/burgers/${id}`, {
     method: "PATCH", // update apanas o que foi alterado
     headers: {"Content-Type" : "application/json"},
     body: dataJson
   });

   const res = await req.json();
  
  //colocar ums msg de sistema.
    this.msg = `Pedido Nº ${res.id} foi atualizado para ${res.status}!`; // linha para mostrar a msg quando for criado o item e mostrar o id.
   
   //limpa msg.
  setTimeout(() => this.msg = "", 3000 );
  
   }
  },

  mounted() {
    this.getPedidos();
  },
};
</script>

<style scoped>
#burger-table {
  max-width: 1200px;
  margin: 0 auto;
}
#burger-table-heading,
.burger-table-row {
  display: flex; /* desse jeito eu coloco os itens na mesma linha, um do lado do outro*/
  flex-wrap: wrap;
}
#burger-table-heading {
  font-weight: bold;
  padding: 12px;
  border-bottom: 3px solid #333;
}
#burger-table-heading div,
.burger-table-row div {
  width: 19%;
}
.burger-table-row {
  width: 100%;
  padding: 12px;
  border-bottom: 1px solid #ccc;
}
#burger-table-heading .order-id,
.burger-table-row .order-number {
  width: 5%;
}
select {
  padding: 12px 6px;
  margin-right: 12px;
}
.delete-btn {
  background-color: #222;
  color: #fcba03;
  border: 2px solid #222;
  padding: 10px;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5sec;
}
.delete-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>
