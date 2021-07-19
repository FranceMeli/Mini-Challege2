<template>
  <div class="mc2Vehicles">
    <h1>MC2Vehicles</h1>
    <b-row>
      <b-col>
        <div id="Map"></div>
        <div id="sliderOre">
          <input id="timeOre" type="range" min="0" max="86400" value="0" step="1"/><br> <!-- max 86400-->
          <span id="rangeOre">00.00.00</span>
        </div>
        <div id="sliderGiorni">
          <input id="timeGiorni" type="range" min="0" max="13" value="0" step="1"/><br>
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
  name: 'MC2Vehicles',
  data() {
    return {
      persons: []
    };
  },
  mounted: function () {
    d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/car-assignments.csv")
        .then((rows) => {
          const ids = rows
              .filter((row) => {
                const entries = d3.values(row);
                return (entries[2].length > 0); // ignore rows with invalid id (not numeric)
              })
              .map((row) => {
                const entries = d3.values(row);
                return {
                  id: entries[2],
                  person: entries[1] + " " +  entries[0],
                };
              });
          this.persons = ids;
        });
    var width = 900;
    var height = 600;
    var inputGiorno = null;
    var inputOra = null;
    const Giorni = ["01/06/2014","01/07/2014", "01/08/2014", "01/09/2014","01/10/2014","01/11/2014","01/12/2014","01/13/2014","01/14/2014",
      "01/15/2014","01/16/2014","01/17/2014","01/18/2014","01/19/2014"];
    const StringOre=[];
    for (let i=0;i<=86400;i=i+1){
      StringOre.push(i)
    }
    let features;
    const riprova = [];
    let projection = d3.geoMercator()
        .scale(460000)
        .center([24.8669975, 36.0699665])
    //  .fitSize([width - 30, height - 30]);
    d3.json("https://raw.githubusercontent.com/FranceMeli/Mini-Challege2/master/static/Geospatial/AbilaT.json")
        .then(function (mapData) {
          features = topojson.feature(mapData, mapData.objects.Abila1);
          createMap(features)
        });
    var svg = d3.select('#Map')
        .append("svg")
        .attr('width', width)
        .attr('height', height);
    //  var person = d3.select(".list-group personList")
    function createMap(features) {
      var geoPath = d3.geoPath().projection(projection);
      /* var bounds = d3.geoBounds(features);
        var centerX = d3.sum(bounds, function(d) {return d[0];}) / 2;
        var centerY = d3.sum(bounds, function(d) {return d[1];}) / 2;*/
      svg.selectAll("path").data(features.features)
          .enter()
          .append("path")
          .attr("d", geoPath)
    }
    //   });

    function createTraj(gps){
      svg
          .selectAll("circle")
          .data(gps.map(function(d) { return d.values}))
          //gps.map(function(d) { return d.values; }))
          .enter()
          .append("circle")
          .attr("class", "move")
          .attr("r", 3)
          .attr("cx", initialCoordinateX)
          .attr("cy", initialCoordinateY)
          .attr("fill", initialDate)
    }

    function dateMatch(d) {
      let B = d3.nest()
          .key( function(d){
            if( d.Timestamp == inputGiorno){
              return d.Timestamp
            }
          }).sortKeys(d3.ascending)
          .entries(d);
      if (B[0].key == inputGiorno) {
        //    this.parentElement.appendChild(this);
        return "red";
      } else {
        return "#00FFFFFF";
      }
    }
    function initialDate(d) {

      var day = d.map(function (d) {
        return d.Timestamp;
      })
      /*  for (let i = 0; i < B.length; i++) {
          if (!Giorni.includes(B[i].Timestamp)) {
            Giorni.push(B[i].Timestamp)
          }*/
      //   var time = d.map(function(d) { return d.Time; })
      if (day[0] == "01/06/2014") {
        this.parentElement.appendChild(this);
        return "red";
      } else {
        return "#999";
      }
    }
    function initialCoordinateX(d){
      var day = d.map(function(d) { return d.Timestamp; })
      if (day[0] == "01/06/2014") {
        //    this.parentElement.appendChild(this);
        var long = d.map(function(d) { return d.long; })
        var lat = d.map(function(d) { return d.lat; })
        return projection([long[0],lat[0]])[0];
      } else {
        return
      }
    }
    function initialCoordinateY(d){
      var day = d.map(function(d) { return d.Timestamp; })
      if (day[0] == "01/06/2014") {
        //    this.parentElement.appendChild(this);
        var long = d.map(function(d) { return d.long; })
        var lat = d.map(function(d) { return d.lat; })
        return projection([long[0],lat[0]])[1];
      } else {
        return
      }
    }

    function Cx(d) {
      let B = d3.nest()
          .key(d => d.Timestamp)
          .entries(d);
      for (let i = 0; i < B.length; i++) {
        if (B[i].key == inputGiorno) {
          //  this.parentElement.appendChild(this);
          var long = B[i].values[0].long
          var lat = B[i].values[0].lat
          return projection([long, lat])[0];
        }
      }
    }
    function Cy(d) {
      /*  var day = d.map(function(d) { return d.Timestamp; })
      if (inputGiorno == day[0]) {*/
      let B = d3.nest()
          .key(d => d.Timestamp)
          .entries(d);
      for (let i = 0; i < B.length; i++) {
        if (B[i].key == inputGiorno) {
          //    this.parentElement.appendChild(this);
          var long = B[i].values[0].long
          var lat = B[i].values[0].lat
          return projection([long,lat])[1];
          /* var long = d.map(function (d) {return d.long;})
           var lat = d.map(function (d) {return d.lat;
           })
           return projection([long[0], lat[0]])[1];*/
        }
      }
    }

    d3.select("#timeGiorni").on("input", function () {
      updateGG(+this.value);
    });
    d3.select("#timeOre").on("input", function () {
      updateOre(+this.value);
    });
// update the fill of each SVG
    function updateGG(value){
      document.getElementById("rangeGiorni").innerHTML = Giorni[value];
      inputGiorno = Giorni[value];
      var g = inputGiorno.replace("/", "-")// d3.select("#timeGiorni"
      var giorno = g.replace("/", "-")
      console.log(giorno)
      d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/GpsForDate/" + giorno + ".csv")
          .then((rows) => {
            const ids = rows
                .map((row) => {
                  const entries = d3.values(row)
                  return {
                    Timestamp: entries["0"],
                    id: entries["1"],
                    lat: entries["2"],
                    long: entries["3"],
                    times: entries["0"],
                  };
                });
            let dataset = [];
            dataset = ids;
            for (let i = 0; i < dataset.length; i = i + 50) {
              let secondi = trasformaOra(dataset[i].Timestamp.slice(11,));
              dataset[i].times = secondi
              dataset[i]["Timestamp"] = dataset[i].Timestamp.slice(0, 10)

              if (dataset[i].Timestamp == "01/06/2014") { //|| dataset[i].Timestamp == "01/07/2014"|| dataset[i].Timestamp == "01/08/2014") { //|| dataset[i].Timestamp.slice(0, 10) == "01/08/2014") {
                riprova.push(dataset[i])
              }
            }
            /*  if (!StringOre.includes(dataset[i].times)) {
              StringOre.push(dataset[i].times);
            }*/
            const trajs = d3.nest()
                .key(d => d.id)
                .entries(riprova);
            createTraj(trajs)
          });
      d3.selectAll(".move")
          .attr("fill", dateMatch)
          .attr("cx", Cx)
          .attr("cy", Cy)
    }
    function updateOre(value){
      let G= document.getElementById("rangeGiorni").innerHTML
      document.getElementById("rangeOre").innerHTML = StringOre[value];
      inputGiorno=G
      inputOra = StringOre[value]
      d3.selectAll(".move")
          .attr("fill", dateMatch)
          .attr("cx", CxTime)
          .attr("cy", CyTime)
    }
    function CxTime(d) {
      let Si=false
      let B = d3.nest()
          .key(function (d) {
            if (d.Timestamp == inputGiorno) {
              return d.Timestamp
            }
          }).sortKeys(d3.ascending)
          .entries(d);
      if (B[0].key == inputGiorno) {
        let lo = B[0].values.map(function (d) {
          //  this.parentElement.appendChild(this);
          if (d.times == inputOra) {
            Si = true;
            let long = d.long
            return long
          }
        })
        let la = B[0].values
            .map(function (d) {
              //  this.parentElement.appendChild(this);
              if (d.times == inputOra) {
                Si = true;
                let lat = d.lat
                return lat
              }
            })
        la.sort(d3.ascending)
        lo.sort(d3.ascending)
        if (Si) {
          return projection([lo[0], la[0]])[0];
        }
      }}
    function CyTime(d) {
      let Si = false
      let B = d3.nest()
          .key(function (d) {
            if (d.Timestamp == inputGiorno) {
              return d.Timestamp
            }
          }).sortKeys(d3.ascending)
          .entries(d);
      if (B[0].key == inputGiorno) {
        let lo = B[0].values.map(function (d) {
          //  this.parentElement.appendChild(this);
          if (d.times == inputOra) {
            Si = true;
            let long = d.long
            return long
          }
        })
        let la = B[0].values.map(function (d) {
          //  this.parentElement.appendChild(this);
          if (d.times == inputOra) {
            Si = true;
            let lat = d.lat
            return lat
          }
        })
        la.sort(d3.ascending)
        lo.sort(d3.ascending)
        if (Si) {
          return projection([lo[0], la[0]])[1];
        }
      }
    }
    function trasformaOra(ora){
      let  Hh = ora.slice(0,2)*3600;
      let  Mm = ora.slice(3,5)*60;
      let  Ss = ora.slice(6,8)*1;
      let totSecondi=Hh+Mm+Ss
      return totSecondi;
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
.personList{
  height: 600px;
  overflow: scroll;
  overflow-style: marquee-block ;
  text-align: center;
}
</style>
