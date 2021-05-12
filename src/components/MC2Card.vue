<template>
  <div class="hello">
    <h1>MC2Card</h1>
    <div id="my_dataviz"></div>
  </div>
</template>

<script>
const d3 = require("d3");
export default {
  name: 'MC2Card',
  mounted() {
    // set the dimensions and margins of the graph
   /* var margin = {top: 20, right: 30, bottom: 40, left: 90},
        width = 460 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;*/
    let Loyalty=[]
    d3.csv("https://raw.githubusercontent.com/FranceMeli/Mini-Challege2/master/static/loyalty_data.csv").then(function(data) {
      for (let i = 0; i < data.length; i++) {
        let type = data[i].id;
        Loyalty.push(type)
      }
      console.log(Loyalty)
    })
  }
}</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>