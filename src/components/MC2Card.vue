<template>
  <div class="mc2Card">
    <h1>MC2Card</h1>
    <div id="Map"></div>
  </div>
</template>

<script>
 const d3 = require("d3");
 const topojson = require("topojson")
//const d3 = require("https://d3js.org/d3.v5.min.js")
// const topojson = require("topojson")
export default {
  name: 'MC2Card',
  mounted() {


    var width = 600,
        height = 300;

    var svg = d3.select('#Map')
        .append("svg")
        .attr('width', width)
        .attr('height', height);



    d3.json("https://raw.githubusercontent.com/FranceMeli/Mini-Challege2/master/static/Geospatial/AbilaT.json").then(function(mapData) {
      var features = topojson.feature(mapData, mapData.objects.Abila1);
   //   var features = mapData.features;
      console.log(features)
      /*var projection = d3.geoIdentity()
          .fitExtent([[50,50],[600-50,300-50]], features)*/
      let geop = d3.geoPath(null)
      console.log(geop)
      svg.append("g")
          .attr("class", "Abila1")
          .selectAll("path")
          .data(mapData.features)
          .enter().append("path")
          .attr("fill", "gray")
          .attr("d", geop);
    });

    //Create SVG element
   /* var svg = d3.select("#Map").append("svg").attr({width:w, height: h});

    //Load in GeoJSON data
    d3.json("https://raw.githubusercontent.com/FranceMeli/Mini-Challege2/master/static/Geospatial/Abila1.json", function(json) {
      console.log("A")
      //Bind data and create one path per GeoJSON feature
      svg.selectAll("path")
          .data(json.features)
          .enter()
          .append("path")
          .attr("d", path)
          .attr("fill","#666666");

    });*/

//Note that when you bind new data, you will be changing existing path elements
//So you would also need to do a exit and modify existing paths


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