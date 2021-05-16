<template>
  <div class="mc2Card">
    <h1>MC2Card</h1>
    <b-row>
      <b-col>
        <div id="Map"></div>
        <div id="sliderOre">
          <input id="timeOre" type="range" min="0" max="8640" value="0" step="1"/><br>
          <span id="rangeOre">00.00.00</span>
        </div>
        <div id="sliderGiorni">
          <input id="timeGiorni" type="range" min="0" max="14" value="0" step="1"/><br>
          <span id="rangeGiorni">01/06/2014</span>
        </div>
      </b-col>
      <b-col>
        <h2>Persons</h2>
        <b-list-group class="personList">
          <b-list-group-item v-for="p in persons" :key="p.id"
                             :variant="p.selected ? 'warning': ''"
                             class="d-flex justify-content-between align-items-center">
            <b-badge variant="primary" pill>{{p.id}}</b-badge>
            {{p.person}}
          </b-list-group-item>
        </b-list-group>
      </b-col>
    </b-row>

  </div>
</template>

<script>
 const d3 = require("d3");
 //const topojson = require("topojson")
//const d3 = require("https://d3js.org/d3.v5.min.js")
const topojson = require("topojson")
export default {
  name: 'MC2Card',
  data() {
    return {
      persons: [],
      timeInterval: 10,
    };
  },
  mounted: function () {
    let A;
    var inputGiorno= null;
 //   var inputOra = null;
    var Giorni = ["01/06/2014", "01/08/2014", "01/09/2014"];
   // var Ore = [""]
    function createMap(features, cities) {
      var width = 900;
      var height = 600;
       /* var bounds = d3.geoBounds(features);
        var centerX = d3.sum(bounds, function(d) {return d[0];}) / 2;
        var centerY = d3.sum(bounds, function(d) {return d[1];}) / 2;*/
         var projection = d3.geoMercator()
            // .scale(460000)
             .center([24.8669975, 36.0699665])
             .fitSize([width - 30, height - 30], features);
      var geoPath = d3.geoPath().projection(projection);
      var svg = d3.select('#Map')
          .append("svg")
          // .attr('id', "mappa")
          .attr('width', width)
          .attr('height', height);
      svg.selectAll("circle")
          .data(cities.map(function(d) { return d.value; }))
          .enter()
          .append("circle")
          .attr("class", "move")
          .attr("r", 3)
          .attr("cx", function(d) {return projection([d.long,d.lat])[0]})
          .attr("cy", function(d) {return projection([d.long,d.lat])[1]})
          .attr("fill", initialDate)
      svg.selectAll("path").data(features.features)
          .enter()
          .append("path")
          .attr("d", geoPath)
    }
    let features;
    const dataset = [];
    const riprova=[];
    d3.json("https://raw.githubusercontent.com/FranceMeli/Mini-Challege2/master/static/Geospatial/AbilaT.json").then(function (mapData) {
      features = topojson.feature(mapData, mapData.objects.Abila1);
      d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/gps.csv").then(function (datas) {
        for (let i = 0; i < datas.length; i++) {
          if (datas[i].id == 1 ){
            dataset.push(datas[i]);
          }
        }
        for (let i = 0; i < dataset.length; i=i+20) {
          if(dataset[i].Timestamp.slice(0,10) == "01/06/2014"){
              riprova.push(dataset[i])
          }
        }
         A = d3.entries(riprova);
        createMap(features, A);
      });
    });
    function dateMatch(d) {
      console.log(d)
      var da = d.Timestamp
      if (inputGiorno == da.slice(0,10)) {
        this.parentElement.appendChild(this);
        return "red";
      } /*else {
        return "#00FFFFFF";
      }*/
    }
    function initialDate(d){
      var da = d.Timestamp
      if (da.slice(0,10) == "01/06/2014") {
        this.parentElement.appendChild(this);
        return "red";
      } /*else {
        return "#999";
      }*/

    }
    d3.select("#timeGiorni").on("input", function() {
      update(+this.value);
    });
// update the fill of each SVG
    function update(value) {
      document.getElementById("rangeGiorni").innerHTML=Giorni[value];
      inputGiorno = Giorni[value];
      d3.selectAll(".move")
          .attr("fill", dateMatch);
    }
  }
}</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
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
  fill: #ccc;
  stroke: #fff;
  stroke-width: .5px;
}

path {
  stroke-width: 1;
  stroke: black;
  opacity: .5;
}
</style>