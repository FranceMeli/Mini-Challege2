<template>
  <div class="hello">
    <h1>MC2Vehicles</h1>
  </div>
</template>

<script>
const d3 = require("d3");
export default {
  name: 'MC2Vehicles',
  mounted() {

    let data = [42, 4, 8, 15, 16, 23];
    d3.select("div")  // We need to get an element
        .append("div").classed("chart", true) // We add the chart graph container element (The div .chart element)
        .selectAll("div") // We select all div but we will get a null selection because their is no div element
        .data(data) // We join the empty selection with the data
        .enter() // Their is no data, therefore only the enter set of the data join contains nodes without element
        .append("div") // We append for each data a div element with the below style
        .style("width", function(d) { return d * 10 + "px"; })
        .text(function(d) { return d; });
    /*d3.tsv('b.txt')
        .then((rows) => {
          const ids = rows
              .filter((row) => {
                const entries = d3.values(row);
                return (entries[0].length > 0); // ignore rows with invalid id (not numeric)
              })
              .map((row) => {
                const entries = d3.values(row);
                return {
                  id: +entries[0],
                  person: entries[1],
                };
              });
          this.persons = ids;

        });
    console.log(p)*/
  }
}
</script>

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
