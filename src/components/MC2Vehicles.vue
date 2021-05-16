<template>
  <div class="hello">
    <h1>MC2Vehicles</h1>
    <div id="EmplType"></div>
    <div id="EmplTitle"></div>
    <div id="movement"></div>
  </div>
</template>

<script>
const d3 = require("d3");
export default {
  name: 'MC2Vehicles',
  mounted() {
    // set the dimensions and margins of the graph
    var margin = {top: 20, right: 30, bottom: 40, left: 90},
        width = 460 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
    var svg = d3.select("#EmplType")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform",
            "translate(" + margin.left + "," + margin.top + ")");
    let EType = []
    let ETitle = []
// Parse the Data
d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/car-assignments.csv").then(function(data) {
  for (let i = 0; i < data.length; i++) {
    let type = data[i].CurrentEmploymentType;
    let title = data[i].CurrentEmploymentTitle;
    EType.push(type)
    ETitle.push(title)
  }
    let EmplType = {};
    let EmplTitle= {}
    for ( let i =0; i < EType.length ; i++ ){
      EmplType[ EType[i] ] = typeof EmplType[ EType[i] ]  === 'undefined' ? 1 : ++EmplType[ EType[i] ];
    }
  for ( let i =0; i < ETitle.length ; i++ ){
    EmplTitle[ ETitle[i] ] = typeof EmplTitle[ ETitle[i] ]  === 'undefined' ? 1 : ++EmplTitle[ ETitle[i] ];
  }

  let  A = d3.entries(EmplType);
  //let B =d3.entries(EmplTitle)
  // Add X axis
      var x = d3.scaleLinear()
          .domain([0, width/10])
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
     const b = svg
          .selectAll("myRect")
          .data(A)
          .enter()
          .append("rect")
        //  .text(function(d) { return d.value; })
          .attr("x", x(0) )
          .attr("y", function(d) { return y(d.key); })
          .attr("width", function(d) {return 10*d.value;})
          .attr("height", y.bandwidth() )
          .attr("fill", "#69b3a2")
     b
      .append("text")
      .attr("fill", "#69b3a2")
      .attr("x", 0)
      .attr("y", 50)
      .attr("height", y.bandwidth() )
      .attr("dy", "0.35em")
      .text(function(d) { return d.value});

      // .attr("x", function(d) { return x(d.value); })
      // .attr("y", function(d) { return y(d.key); })
      // .attr("width", x.bandwidth())
      // .attr("height", function(d) { return height - y(d.value); })
      // .attr("fill", "#69b3a2")

    })
    let mov = d3.select("#movement")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform",
            "translate(" + margin.left + "," + margin.top + ")");
    let B=[]
    d3.csv("https://raw.githubusercontent.com/FranceMeli/Mini-Challege2/master/static/gps.csv").then(function(data) {
      for (let i = 0; i < data.length; i++) {
        let type = data[i].id;
        B.push(type)
      }
      let Move = {};
      for ( let i =0; i < B.length ; i++ ){
        Move[ B[i] ] = typeof Move[ B[i] ]  === 'undefined' ? 1 : ++Move[ B[i] ];
      }
      let C =d3.entries(Move)

      var x = d3.scaleLinear()
          .domain([0, width*100])
          .range([ 0, width]);
      mov.append("g")
          .attr("transform", "translate(0," + height + ")")
          .call(d3.axisBottom(x))
          .selectAll("text")
          .attr("transform", "translate(-10,0)rotate(-45)")
          .style("text-anchor", "end");

      // Y axis
      var y = d3.scaleBand()
          .range([ 0, height ])
          .domain(C.map(function(d) { return d.key; }))
          .padding(.1);
      mov.append("g")
          .call(d3.axisLeft(y))

      //Bars
      const b = mov
          .selectAll("myRect")
          .data(C)
          .enter()
          .append("rect")
          //  .text(function(d) { return d.value; })
          .attr("x", x(0) )
          .attr("y", function(d) { return y(d.key); })
          .attr("width", function(d) {return d.value/100;})
          .attr("height", y.bandwidth() )
          .attr("fill", "#69b3a2")
      b
          .append("text")
          .attr("fill", "#69b3a2")
          .attr("x", 0)
          .attr("y", 50)
          .attr("height", y.bandwidth() )
          .attr("dy", "0.35em")
          .text(function(d) { return d.value});
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

</style>
