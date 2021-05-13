<template>
  <div class="mc2Card">
    <h1>MC2Card</h1>
    <div id="Map"></div>
  </div>
</template>

<script>
const d3 = require("d3");
export default {
  name: 'MC2Card',
  mounted() {
   /* d3.json("https://raw.githubusercontent.com/FranceMeli/Mini-Challege2/master/static/Geospatial/Abila.json").then(function(data){ console.log(data)});
*/
 /*   var width = 1100;
    var height = 800;

    var svg = d3.select("#Map")
        .append("svg")
        .attr("width",width)  // apply width,height to svg
        .attr("height",height);



    d3.json("https://raw.githubusercontent.com/FranceMeli/Mini-Challege2/master/static/Geospatial/Abila.json", function(err, geojson) {
    console.log("AAA")
      var projection = d3.geoMercator();
      var path = d3.geoPath().projection(projection);
      projection.fitSize([width,height],geojson); // adjust the projection to the features
      svg.append("path").attr("d", path(geojson)); // draw the features

    })*/
    var w = 500;
    var h = 300;

    //Define path generator
   let  projection = d3.geoMercator()
    var path = d3.geo.path(projection);

    //Create SVG element
    var svg = d3.select("#Map").append("svg").attr({width:w, height: h});

    //Load in GeoJSON data
    d3.json("https://raw.githubusercontent.com/FranceMeli/Mini-Challege2/master/static/Geospatial/Abila.json", function(json) {
      console.log("A")
      //Bind data and create one path per GeoJSON feature
      svg.selectAll("path")
          .data(json.features)
          .enter()
          .append("path")
          .attr("d", path)
          .attr("fill","#666666");

    });

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