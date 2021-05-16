<template>
  <div id="app">
    <h1>VC 2008</h1>
    <h2>Mini-Challenge 2</h2>
    <b-navbar toggleable="lg" type="dark" variant="info">
      <!--  <b-navbar-brand href="#">VC 2008</b-navbar-brand>-->
      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>
      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item href="#">General Results</b-nav-item>
          <b-nav-item href="#">Vehicles</b-nav-item>
          <b-nav-item href="#">Credit-Cards</b-nav-item>
        </b-navbar-nav>
      </b-collapse>
   </b-navbar>
  <!--  <MC2Vehicles></MC2Vehicles>-->
    <MC2Card></MC2Card>
  </div>
</template>

<script>
//import MC2Vehicles from './components/MC2Vehicles.vue'
import MC2Card from './components/MC2Card.vue'
//const d3 = require("d3");
export default {
  name: 'App',
  components: {
    MC2Card,
 //   MC2Vehicles
  },
  mounted() {

  }

}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  /*color: #2c3e50;*/
  margin-top: 60px;
}
</style>
