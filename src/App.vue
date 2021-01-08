<template>
  <Header msg="Welcome to Your Vue.js App"/>
  <Form/>
</template>

<script>
import Header from "@/components/Header";
import Form from "@/components/Form";


export default {
  name: 'App',
  components: {
    Header,
    Form
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Sriracha&display=swap');
#app {
  font-family: 'Sriracha', cursive;
  font-weight:normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
}
</style>
