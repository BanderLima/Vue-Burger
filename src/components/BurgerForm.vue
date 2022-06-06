<template>
  <div>
    <!--Esta é a props MSG criada dentro do componente message-->
    <!--esse codigo abaixo está usando o v-bind para uso do msg, e o v-show para condicionar quando sera mostrada essa msg, dessa maneira, logo de cara, a msg na tela ja some.-->
    <!--após é necessário criar a condição dentro do metodo de criação-->
    <Message :msg="msg" v-show="msg"/>
  </div>
  <div>
      <!--aqui é criado o metodo para enviar os dados do burger, metodo criado la em baixo com esse nome do evento-->
    <form id="burger-form" @submit="createBurger">
      <div class="input-container">
        <label for="nome">Nome do Cliente</label>
        <input
          type="text"
          id="nome"
          name="nome"
          v-model="nome"
          placeholder="Digite o seu nome"
        />
      </div>
      <div class="input-container">
        <label for="pao">Escolha o pão: </label>
        <select name="pao" id="pao" v-model="pao">
          <option value="">Selecione seu pão</option>
          <!--nessa parte e inserido o v-for que fará a mágica, junto com v-bind-->
          <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">
            {{ pao.tipo }}
          </option>
        </select>
      </div>
      <div class="input-container">
        <label for="carne">Escolha a carne do seu Burger: </label>
        <select name="carne" id="carne" v-model="carne">
          <option value="">Selecione o tipo de carne</option>
          <!--mesmo codigo acima, referenciando a chave que faz ligação com backend e interpolando o texto do tipo dentro dos options-->
          <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">
            {{ carne.tipo }}
          </option>
        </select>
      </div>
      <div id="opcionais-container" class="input-container">
        <label id="opcionais-title" for="opcionais"
          >Selecione os opcionais:
        </label>
        <!--aqui eu quero q se repita a div com os opcionais, entao o v-for vai dentro da div-->
        <div
          class="checkbox-container"
          v-for="opcional in opcionaisdata"
          :key="opcional.id"
        >
          <input
            type="checkbox"
            name="opcionais"
            v-model="opcionais"
            :value="opcional.tipo"
          />
          <span>{{ opcional.tipo }}</span>
        </div>
      </div>
      <div class="input-container">
        <input type="submit" class="submit-btn" value="Criar meu Burger!" />
      </div>
    </form>
  </div>
</template>

<script>
import Message from './Message.vue';


export default {
  name: "BurgerForm",
  components:{
    Message
  },
  data() { // todo data retorna algo....
    return { //aqui ficam os dados q quero que retorne, inicialmente nullos
      paes: null,
      carnes: null,
      opcionaisdata: null,
      pao: null,
      carne: null,
      opcionais: [], // isso é um array.
      msg: null,
    };
  },

  methods: {
    //esse metodo esta fazendo um get via localhost do banco de dados/ingredientes, recebendo os dados em json e armazenando os dados.
    async getIngredientes() {
      const req = await fetch("http://localhost:3000/ingredientes"); // acessando o banco de dados fake...
      const data = await req.json(); // esperando a requisição em json, recebe em json.

      this.paes = data.paes; // pegando os dados de dentro de data.paes e armazenando dentro do paes.
      this.carnes = data.carnes;
      this.opcionaisdata = data.opcionais; // mesma coisa, pegando o dado dentro de opcionais e armazenando em opcionaisdata.

      // console.log(data);
    },
    async createBurger(e){  //metodo de criação 
      
      e.preventDefault(); //esse codigo evita o envio do formulario
    //console.log("criado") // apenas testes de funcionamento

    const data = {
      nome: this.nome, // deste modo o nome armazena o dado digitado pelo usuário
      carne: this.carne,
      pao: this.pao,
      //note que o data opcionais é um array [], entao é necessario indicar dessa forma para q funcione.
      opcionais: Array.from(this.opcionais),  
      status: "Solicitado" // aqui é apenas um ajuste para retorno desse mensagem, um backend real faria essa função.
    }
    // console.log(data) teste do data para ver se esta mandando os itens de forma correta.
    const dataJson = JSON.stringify(data); // aqui esta tranformando os dados do data de json para texto,
    //criando o req de requerimento, para acessar o backend nessa porta padrao, na pasta burger e criando o metodo dentro para envio dos dados criados.
    const req = await fetch("http://localhost:3000/burgers" ,{
      method: "POST",  // para evitar que mande os dados como GET
      headers: {"Content-Type": "application/json"}, // essa parte para comunicação com json.
      body: dataJson  // e essa parte envia os dados do corpo (data) como texto
    });
    const res = await req.json(); //resultado do requerimento em json em texto sendo armazenado em res.
   // console.log(res) //e vendo no console os dados se estao corretos.

   //colocar ums msg de sistema.
    this.msg = `Pedido Nº ${res.id} realizado com sucesso`; // linha para mostrar a msg quando for criado o item e mostrar o id.
   
   //limpa msg.
  setTimeout(() => this.msg = "", 3000 );

   //Limpa dados

   this.nome = "";
   this.carne = "";
   this.pao = "";
   this.opcionais = "";

  }
  },
  
  mounted() { // esse é p metodo padrao para montar, sem ele nao roda.
    this.getIngredientes();
  },
};
</script>

<!--CSS-->
<style scoped>
#burger-form {
  max-width: 400px; /*largura máxima */
  margin: 0 auto; /*centralizando */
}
.input-container {
  display: flex;
  flex-direction: column; /*a label fica em uma linha e o input noutra, tem q ter o display flex. */
  margin-bottom: 20px;
}
label {
  font-weight: bold;
  margin-bottom: 15px;
  color: #222;
  padding: 5px 10px;
  border-left: 4px solid #fcba03;
}
input,
select {
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
  color: #fcba03;
  font-weight: bold;
  border: 2px solid #222;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.05s;
}
.submit-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>
