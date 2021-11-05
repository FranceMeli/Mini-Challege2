<template>
  <div id="app">
    <h1>VC 2008</h1>
    <h2>Mini-Challenge 2</h2>
    <b-navbar toggleable="lg" type="dark" variant="info">
      <!--  <b-navbar-brand href="#">VC 2008</b-navbar-brand>-->
      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>
      <b-collapse id="nav-collapse" is-nav >
        <b-navbar-nav>
          <b-nav-item href="#" @click="selezionato='Home'">Home</b-nav-item>
          <b-nav-item href="#" @click="selezionato='Vehicles'">Vehicles</b-nav-item>
          <b-nav-item href="#" @click="selezionato='Card'">Credit-Cards</b-nav-item>
        </b-navbar-nav>
      </b-collapse>
   </b-navbar>
   <component :is="selezionato"></component>
<!--   <MC2Vehicles id="v"></MC2Vehicles>
    <MC2Card id="c"></MC2Card>-->
    </div>
  </template>

  <script>
  //const d3 = require("d3");
  import MC2Vehicles from './components/MC2Vehicles.vue'
  import MC2Card from "@/components/MC2Card";
  import Home from "@/components/Home";
  //import MC2Card from './components/MC2Card.vue'
  export default {
    name: 'App',
    data() {
return {
      selezionato: 'Home'
    };
},
    components: {
     'Card': MC2Card,
     'Vehicles': MC2Vehicles,
      'Home': Home
    },
    methods: {
    },
    mounted: function () {
/*  //GraficoPie Chart
      var width = 400
      var height = 400
      var margin = 20
      var radius = Math.min(width, height) / 2 - margin ;
      var svg = d3.select('#EmplType')
          .append("svg")
          .attr("width", width)
          .attr("height", height)
          .append("g")
          .attr("transform", "translate(" + width / 2 + "," + height / 2 + ")");
      d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/car-assignments.csv")
          .then((rows) => {
            const ids = rows
                .map((row) => {
                  const entries = d3.values(row)
                  return {
                    Cognome: entries["0"],
                    Nome: entries["1"],
                    Id: entries["2"],
                    LavoroCorrente: entries["3"],
                    TipoLavoro: entries["4"],
                  };
                });
            let dataset = [];
            dataset = ids;
            const da = d3.nest()
                .key(function (d) {
                  return d.LavoroCorrente;
                })
                .rollup(function (v) {
                  return v.length;
                })
                .entries(dataset);
            var pie = d3.pie()
                .value(function (d) {
                  return d.value.value;
                })
            var data_ready = pie(d3.entries(da))
// Now I know that group A goes from 0 degrees to x degrees and so on.
            var color = d3.scaleOrdinal()
                .domain(data_ready)
                .range(["gold", "blue", "green", "red", "pink"]);
// shape helper to build arcs:
            var arcGenerator = d3.arc()
                .innerRadius(0)
                .outerRadius(radius)
// Build the pie chart: Basically, each part of the pie is a path that we build using the arc function.
            svg
                .selectAll('mySlices')
                .data(data_ready)
                .enter()
                .append('path')
                .attr('d', arcGenerator)
                .attr('fill', function (d) {
                  return (color(d.data.key))
                })
                .attr("stroke", "black")
                .style("stroke-width", "2px")
                .style("opacity", 0.7)
// Now add the annotation. Use the centroid method to get the best coordinates
            svg
                .selectAll('mySlices')
                .data(data_ready)
                .enter()
                .append('text')
                .text(function (d) {
                  var p = d.data.value.value
                  return d.data.value.key + " " + Math.floor((p * 100) / 60) + "" + "%"
                })
                .attr("transform", function (d) {
                  return "translate(" + arcGenerator.centroid(d) + ")";
                })
                .style("text-anchor", "middle")
                .style("font-size", 14)
          });

  // Grafico 3
/!*  var margin1 = {top: 20, right: 30, bottom: 40, left: 90},
      width1 = 460 - margin1.left - margin1.right,
      height1 = 400 - margin1.top - margin1.bottom;
// append the svg object to the body of the page
  /!* var svg1 = d3.select("#EmplTitle")
      .append("svg")
      .attr("width", width1)
      .attr("height", height1)
      .append("g")
      .attr("transform", "translate(" + margin1.left + "," + margin1.top + ")");*!/
  let EType = []
  let ETitle = []*!/
// Parse the Data
  d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/car-assignments.csv")
    //  .then(function(data) {
      .then((rows) => {
          const ids = rows
              .map((row) => {
                const entries = d3.values(row)
                if (entries["2"]> 0) {
                  return {
                     Employer: entries["1"] + " " + entries["0"],
                    Id: entries["2"],
                     Type: entries["3"],
                    Title: entries["4"],
                    Car: "Yes"
                }
                }
                else {
                  return {
                    Employer: entries["1"] + " " + entries["0"],
                    Id: entries["2"],
                    Type: entries["3"],
                    Title: entries["4"],
                    Car: "No"
                  }
                }
              });
       let dataset = ids;
/!*
        var nested = d3.nest()
            .key(function(d) { return d.Type; })
            .key(function(d) { return d.Title; })
            .rollup(function(leaves) { return leaves.length; })
            .entries(dataset);
*!/
        tabulate(dataset, ['Employer','Id', 'Title','Car']); // 2 column table
   /!* for (let i = 0; i < dataset.length; i++) {
      let type = dataset[i].Type;
      let title = dataset[i].Title;
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
    let  A = d3.entries(EmplType);*!/
    //let B =d3.entries(EmplTitle)
    // Add X axis


  /!*  var x = d3.scaleLinear()
        .domain([0, width1/10])
        .range([ 0, width1]);
    svg1.append("g")
        .attr("transform", "translate(0," + height1 + ")")
        .call(d3.axisBottom(x))
        .selectAll("text")
        .attr("transform", "translate(-10,0)rotate(-45)")
        .style("text-anchor", "end");
    // Y axis
    var y = d3.scaleBand()
        .range([ 0, height1 ])
        .domain(A.map(function(d) { return d.key; }))
        .padding(.1);

    svg1.append("g")
        .call(d3.axisLeft(y))
    //Bars
    const b = svg1
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
        .text(function(d) { return d.value});*!/

    // .attr("x", function(d) { return x(d.value); })
    // .attr("y", function(d) { return y(d.key); })
    // .attr("width", x.bandwidth())
    // .attr("height", function(d) { return height - y(d.value); })
    // .attr("fill", "#69b3a2")

  })
      function tabulate(data, columns) {
        var table = d3.select('#EmplTitle')
            .append('table')
            .attr("id", "table")

        var thead = table.append('thead')
        var	tbody = table.append('tbody');

        // append the header row
        thead.append('tr')
            .selectAll('th')
            .data(columns).enter()
            .append('th')
            .text(function (column) { return column; });

        // create a row for each object in the data
        var rows = tbody.selectAll('tr')
            .data(data)
            .enter()
            .append('tr')
            .style('background-color', Color)
        //    .style('opacity', "0.6");

        // create a cell in each row for each column
         rows.selectAll('td')
            .data(function (row) {
              return columns.map(function (column) {
                return {column: column, value: row[column]};
              });
            })
            .enter()
            .append('td')
            // .style('background-color', Color)
            // .style('opacity', "0.6")
            .text(function (d) { return d.value; });
        return table;

        function Color(d){
          if (d.Type=="Facilities"){
            return "rgba(255,255,000,0.3)";
          }
          if (d.Type=="Information Technology"){
            return "rgba(000,000,255,0.3)";
          }
          if (d.Type=="Engineering"){
            return "rgba(000,225,000, 0.3)";
          }
          if (d.Type=="Security"){
            return "rgba(255,000,255, 0.3)";
          }
          if (d.Type=="Executive"){
            return "rgba(255,000,000, 0.3)";
          }
        }
      }*/
// render the tables
  }

}
</script>

  <style>
  #v {
    display: none;
  }
  #c {
    display: none;
  }
  #table {
    font-family: Arial, Helvetica, sans-serif;
    width: 100%;
    overflow: scroll;
    overflow-style: auto;
    text-align: left;
    border-color: black ;
  }
  #table td {
     border: 1px solid #ddd;
     padding: 6px;
   }
  #table th {
    padding-top: 10px;
    padding-bottom: 10px;
    text-align: center;
    background-color: #04AA6D;
    color: white;
  }
  td {
    padding: 1px 4px;
  }
  #EmplType{
    width:49%;
    display: inline-block;
    float: right;
  }
  #EmplTitle{
    width:49%;
    display: inline-block;
    overflow: scroll;
    height: 410px;
  }
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    /*color: #2c3e50;*/
    margin-top: 60px;
  }
  </style>
