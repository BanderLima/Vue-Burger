<template>
  <div class="message-container">
    <p>{{msg}}</p>

  </div>
</template>

<script>
export default {
  name: "Message",
  props: {  // criando uma props, falando q o conteudo dentro de msg é uma string.
    msg: String
  }
}
</script>

<style scoped>
.message-container {
  color: #004085;
  background-color: #cce5ff;
  border: 2px solid #b8daff;
  border-radius: 5px;
  padding: 10px;
  max-width: 400px;
  margin: 0 auto;
  margin-bottom: 10px;

}
</style>