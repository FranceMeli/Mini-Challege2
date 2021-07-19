<template>
  <div class="hello">
    <h1>MC2Card</h1>
    <div id="EmplType"></div>
    <div id="EmplTitle"></div>
    <button v-on:click="update('var1')">Variable 1</button>
    <button v-on:click="update('var2')">Variable 2</button>
    <div id="movement"></div>
  </div>
</template>

<script>
const d3 = require("d3");
export default {
  name: 'MC2Card',
  methods: {
      update: function (selectedVar) {
        var margin1 = {top: 10, right: 30, bottom: 20, left: 50},
            width1 = 460 - margin1.left - margin1.right,
            height1 = 400 - margin1.top - margin1.bottom;
// append the svg object to the body of the page
        var svg1 = d3.select("#movement")
            .append("svg")
            .attr("width", width1 + margin1.left + margin1.right)
            .attr("height", height1 + margin1.top + margin1.bottom)
            .append("g")
            .attr("transform",
                "translate(" + margin1.left + "," + margin1.top + ")");
        var x = d3.scaleBand()
            .range([ 0, width1 ])
            .padding(0.2);
        var xAxis = svg1.append("g")
            .attr("transform", "translate(0," + height1 + ")")

// Initialize the Y axis
        var y = d3.scaleLinear()
            .range([ height1, 0]);
        var yAxis = svg1.append("g")
            .attr("class", "myYaxis")
    let Data=[];
    if (selectedVar == 'var1') {
      // Parse the Data
      d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/cc_data.csv")
          .then((rows) => {
            const ids = rows
                .map((row) => {
                  const entries = d3.values(row)
                  return {
                    Timestamp: entries["0"],
                    Luogo: entries["1"],
                    Prezzo: entries["2"],
                    Nome: entries["3"],
                    Cognome: entries["4"],
                  };
                });
            let dataset;
            dataset = ids;
            let data = [];
            for (let i = 0; i < dataset.length; i = i + 20) {
              data.push(dataset[i])
            }
          let data1 = data;
          })
       data2 = data1;
    }
          else if (selectedVar == 'var2') {
        // Parse the Data
      d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/loyalty_data.csv")
            .then((rows) => {
              const ids = rows
                  .map((row) => {
                    const entries = d3.values(row)
                    return {
                      Timestamp: entries["0"].slice(0, 8),
                      Luogo: entries["1"],
                      Prezzo: entries["2"],
                      Nome: entries["3"],
                      Cognome: entries["4"],
                    };
                  });
              let dataset = [];
              dataset = ids;
              let data = [];
              for (let i = 0; i < dataset.length; i = i + 20) {
                data.push(dataset[i])
              }
            })
      }
        console.log(data)
        for (let i = 0; i < Data.length; i++) {
          console.log(Data[i])
        }
            x.domain(d3.entries(Data).map(function (d) {
              return d.Cognome;
            }))
            xAxis.transition().duration(1000).call(d3.axisBottom(x))

            // Add Y axis
            y.domain([0, 400]);
            yAxis.transition().duration(1000).call(d3.axisLeft(y));

            // variable u: map data to existing bars
            var u = svg1.selectAll("rect")
                .data(Data)
            // update bars
            u
                .enter()
                .append("rect")
                .merge(u)
                .transition()
                .duration(1000)
                .attr("x", function (d) {
                  return x(d.Cognome);
                })
                .attr("y", function (d) {
                  return y(d.Prezzo);
                })
                .attr("width", x.bandwidth())
                .attr("height", function (d) {
                  return height1 - y(d.Prezzo);
                })
                .attr("fill", "#69b3a2")

    }
// Initialize plot
  },
  mounted() {
    //Grafico 1
/*    var width = 450
   var height = 450
    var margin = 40
    var radius = Math.min(width, height) / 2 - margin
    var svg = d3.select("#EmplType")
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
     //   console.log(data_ready[0].data.value)
// Now I know that group A goes from 0 degrees to x degrees and so on.
        var color = d3.scaleOrdinal()
            .domain(data_ready)
            .range(d3.schemeSet2);
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
      });*/
   /// Prova altro dataset Grafico 2
  /*  var margin1 = {top: 10, right: 30, bottom: 20, left: 50},
        width1 = 460 - margin1.left - margin1.right,
        height1 = 400 - margin1.top - margin1.bottom;
// append the svg object to the body of the page

    var svg1 = d3.select("#movement")
        .append("svg")
        .attr("width", width1 + margin1.left + margin1.right)
        .attr("height", height1 + margin1.top + margin1.bottom)
        .append("g")
        .attr("transform",
            "translate(" + margin1.left + "," + margin1.top + ")");
// Parse the Data
    d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/cc_data.csv")
        .then((rows) => {
          const ids = rows
              .map((row) => {
                const entries = d3.values(row)
                return {
                  Timestamp: entries["0"].slice(0,8),
                  Luogo: entries["1"],
                  Prezzo: entries["2"],
                //  Nome: entries["3"],
                //  Cognome: entries["4"],
                };
              });
          let dataset = [];
          dataset=ids;
          let data=[];
          for (let i = 0; i < dataset.length; i=i+20) {
            data.push(dataset[i])
            }
          var groups = d3.map(data, function(d){return(d.Timestamp)})
      // List of subgroups = header of the csv files = soil condition here
      var subgroups = d3.map(data, function(d){return(d.Luogo)})
          let subg = [];
          for (let i = 0; i < subgroups.length; i++) {
            if( !subg.includes(subgroups[i]))
                subg.push(subgroups[i])
          }

      // List of groups = species here = value of the first column called group -> I show them on the X axis

      // Add X axis
      var x = d3.scaleBand()
          .domain(groups)
          .range([0, width1])
          .padding([0.2])
      svg1.append("g")
          .attr("transform", "translate(0," + height1 + ")")
          .call(d3.axisBottom(x).tickSizeOuter(10));

      // Add Y axis
      var y = d3.scaleLinear()
          .domain([0, 120])
          .range([ height1, 0 ]);
      svg1.append("g")
          .call(d3.axisLeft(y));

      // color palette = one color per subgroup
      var color = d3.scaleOrdinal()
          .domain(subg)
          .range(d3.schemeSet2);

      //stack the data? --> stack per subgroup


          var e = d3.nest()
              .key(function(d) { return d.Timestamp; })
              .key(function(d) { return d.Luogo; })
              .rollup(function(v) { return d3.sum(v, function(d) { return d.Prezzo; }); })
              .entries(data);
          console.log(e);
       //   console.log(d3.entries(e));
          let tot1 = []
          let wrongMap = []
          for (let i=0; i<e.length; i++){
              d3.values(e[i].values).map(function (d) {
                wrongMap["Timestamp"] = e[i].key
                wrongMap[d.key] = d.value
              });
            tot1.push(wrongMap)
            wrongMap=[]
          }
          for( let j=0; j<tot1.length; j++) {
            console.log(tot1[j])
            if (subg.includes(tot1[j]) == false) {
              wrongMap[subg[j]] = 0
              tot1.push(wrongMap[j]);
              wrongMap = []
            }
          }
       //   console.log(tot1)
     //     console.log(date);

/!*          let date=[];
          let si = false;
          for (let k=0; k<14; k++){
            for (let i=0; i<data.length; i++){
              if(date.includes(data[i].Timestamp == si)){
                var u = d3.entries(data[i]).map( function (d){
                  const entries = d3.values(d)
                  return {
                    Timestamp: entries["1"].Timestamp,
                    Luogo: entries["1"],
                    Prezzo: entries["1"],
                    Nome: entries["1"],
                    Cognome: entries["1"],
                  };
                });
              }
              date=[];
              date.push(u);
            }
          }
       // console.log(date);
          let time=[]
          for (let i=0; i<data.length; i++){
            let a =data[i].Timestamp
            if (time.includes(a)==false){
              time.push(a)
            }
          }

          let dates=[]
          for (let i=0; i<data.length; i++){
            let a =data[i].Timestamp

            if (dates.length == 0) {
              dates.push(a)
              dates.push(data[i].)
            }
            else if(dates.includes(a)== false){
              dates.push(a)
            }

          }*!/
     //   console.log(data)
     //   console.log(subgroups);
        var stackedData = d3.stack()
              .keys(subg)
      var stacked = stackedData(tot1)
    //  console.log(stacked)
      // ----------------
      // Highlight a specific subgroup when hovered
      // ----------------

      // What happens when user hover a bar
      var mouseover = function() {
        // what subgroup are we hovering?
        var subgroupName = d3.select(this.parentNode).datum().key; // This was the tricky part

      //  var subgroupValue = d.data[subgroupName];
        // Reduce opacity of all rect to 0.2
        d3.selectAll(".myRect").style("opacity", 0.2)
        // Highlight all rects of this subgroup with opacity 0.8. It is possible to select them since they have a specific class = their name.
        d3.selectAll("."+subgroupName)
            .style("opacity", 1)
      }

      // When user do not hover anymore
      var mouseleave = function() {
        // Back to normal opacity: 0.8
        d3.selectAll(".myRect")
            .style("opacity",0.8)
      }

      // Show the bars
      svg1.append("g")
          .selectAll("g")
          // Enter in the stack data = loop key per key = group per group
          .data(stacked)
          .enter().append("g")
          .attr("fill", function(d) { return color(d.key); })
          .attr("class", function(d){ return "myRect " + d.key }) // Add a class to each subgroup: their name
          .selectAll("rect")
          // enter a second time = loop subgroup per subgroup to add all rectangles
          .data(function(d) { return d; })
          .enter().append("rect")
          .attr("x", function(d) { return x(d.data.group); })
          .attr("y", function(d) { return y(d[1]); })
          .attr("height", function(d) { return y(d[0]) - y(d[1]); })
          .attr("width",x.bandwidth())
          .attr("stroke", "grey")
          .on("mouseover", mouseover)
          .on("mouseleave", mouseleave)
         // console.log(y)

    })*/
   // Grafico 3
    /*var margin = {top: 20, right: 30, bottom: 40, left: 90},
        width = 460 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
     svg = d3.select("#EmplTitle")
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
    }) */
    //Grafico 4
    // Parse the Data
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