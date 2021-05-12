<template>
  <div class="mc2Card">
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
    data = d3.json('https://gist.githubusercontent.com/jdev42092/5c285c4a3608eb9f9864f5da27db4e49/raw/a1c33b1432ca2948f14f656cc14c7c7335f78d95/boston_neighborhoods.json')
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