<template>
  <div class="hello">
    <h1>MC2Vehicles</h1>
    <div id="my_dataviz"></div>
  </div>
</template>

<script>
const d3 = require("d3");
export default {
  name: 'MC2Vehicles',
  mounted() {

    let a = []
    // set the dimensions and margins of the graph
    var margin = {top: 20, right: 30, bottom: 40, left: 90},
        width = 460 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
    var svg = d3.select("#my_dataviz")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform",
            "translate(" + margin.left + "," + margin.top + ")");

// Parse the Data
d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/car-assignments.csv").then(function(data) {
  for (let i = 0; i < data.length; i++) {
    let el = data[i].CurrentEmploymentType;
    a.push(el)
  }
  console.log(a)
    let map = {};
    for ( let i =0; i < a.length ; i++ ){
      map[ a[i] ] = typeof map[ a[i] ]  === 'undefined' ? 1 : ++map[ a[i] ];
    }
    console.log(map);
  let  A = d3.entries(map);
  console.log(A)
  // Add X axis
      var x = d3.scaleLinear()
          .domain([0, 20])
          .range([ 0, width]);
      svg.append("g")
          .attr("transform", "translate(0," + height + ")")
          .call(d3.axisBottom(x))
          .selectAll("text")
          .attr("transform", "translate(-10,0)rotate(-45)")
          .style("text-anchor", "end");

      // Y axis
      var y = d3.scaleBand()
          .range([ 0, height ])
          .domain(A.map(function(d) { return d.key; }))
          .padding(.1);
      svg.append("g")
          .call(d3.axisLeft(y))

      //Bars
      svg.selectAll("myRect")
          .data(A)
          .enter()
          .append("rect")
          .attr("x", x(0) )
          .attr("y", function(d) { return y(d.key); })
          .attr("width", function(d) {return d.value;})
          .attr("height", y.bandwidth() )
          .attr("fill", "#69b3a2")


      // .attr("x", function(d) { return x(d.key); })
      // .attr("y", function(d) { return y(d.value); })
      // .attr("width", x.bandwidth())
      // .attr("height", function(d) { return height - y(d.Value); })
      // .attr("fill", "#69b3a2")

    })
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
