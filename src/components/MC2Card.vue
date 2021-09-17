<template>
  <div class="hello">
    <h1>MC2Card</h1>
    <div id="spese">
      <p>Spese con Credit Card e Loyalty Card</p>
      <button id="var1">Credit Card</button>
      <button id="var2">Loyalty Card</button>
      <div id="movement"></div>
    </div>
    <div id="appunti">
      <p>Dipendenti con spese oltre i 100$, in rosso i possibili outliers</p>
    </div>
    <div id="luogo"></div>
    <div id="patterns"></div>
  </div>
</template>

<script>
const d3 = require("d3");
export default {
  name: 'MC2Card',
  // Initialize plot
  mounted() {
      var margin1 = {top: 30, right: 30, bottom: 20, left: 120},
          width1 = 600,
          height1 = 530;
// append the svg object to the body of the page
      var svg1 = d3.select("#movement")
          .append("svg")
          .attr("width", width1 + margin1.left + margin1.right)
          .attr("height", height1 + margin1.top + margin1.bottom)
          .append("g")
          .attr("transform",
              "translate(" + margin1.left + "," + margin1.top + ")");
    var svg2 = d3.select("#luogo")
        .append("svg")
        .attr("width", width1 + margin1.left + margin1.right)
        .attr("height", height1 + margin1.top + margin1.bottom)
        .append("g")
        .attr("transform",
            "translate(" + margin1.left + "," + margin1.top + ")");
    var svg3 = d3.select("#patterns")
        .append("svg")
        .attr("width", width1 + margin1.left + margin1.right)
        .attr("height", height1 + margin1.top + margin1.bottom)
        .append("g")
        .attr("transform",
            "translate(" + margin1.left + "," + margin1.top + ")");
      var x = d3.scaleLinear()
          .range([0, width1])
      //  .padding(0.2);
      var xAxis = svg1.append("g")
          .attr("transform", "translate(0," + height1 + ")")
// Initialize the Y axis
      var y = d3.scaleBand()
          .range([0, height1])
      //   .padding(0.2);
      var yAxis = svg1.append("g")
          //  .attr("transform", "translate(0," + height1 + ")")
          .attr("class", "myYaxis")
    var x1 = d3.scaleLinear()
        .range([0, width1])
    //  .padding(0.2);
    var xAxis1 = svg2.append("g")
        .attr("transform", "translate(0," + height1 + ")")
// Initialize the Y axis
    var y1 = d3.scaleBand()
        .range([0, height1])
    //   .padding(0.2);
    var yAxis1 = svg2.append("g")
        //  .attr("transform", "translate(0," + height1 + ")")
        .attr("class", "myYaxis1")
    var x2 = d3.scaleBand()
        .range([0, width1])
    //  .padding(0.2);
    var xAxis2 = svg3.append("g")
        .attr("transform", "translate(0," + height1 + ")")
// Initialize the Y axis
    var y2 = d3.scaleLinear()
        .range([height1, 0])
    //   .padding(0.2);
    var yAxis2 = svg3.append("g")
        //  .attr("transform", "translate(0," + height1 + ")")
        .attr("class", "myYaxis2")
      var table = d3.select('#appunti')
          .append('table')
          .attr("id", "table")
      //  var thead = table.append('thead')
      var tbody = table.append('tbody');
      update("var1")
      d3.select("#var1").on("click", function () {
        update("var1")
      });
      d3.select("#var2").on("click", function () {
        update("var2")
      });

    d3.select(".tab").on("click", function () {
      update("var2")
    });
      function update(selectedVar) {
        let persons=[]
        d3.csv("https://raw.githubusercontent.com/FranceMeli/progetto-minichallenges/master/static/car-assignments.csv")
            .then((rows) => {
              const ids = rows
                  .map((row) => {
                    const entries = d3.values(row)
                    return {
                      Employer: entries["1"] + " " + entries["0"],
                    };
                  });
              persons = ids
              persons.sort(function (a,b) {return d3.ascending(a.Employer, b.Employer);});
            })
        if (selectedVar === 'var1') {
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
                        Nome: entries["3"] + " " + entries["4"]
                      };
                    });
                let dataset;
                dataset = ids;
                provo(dataset);
                secondo(dataset)
              })
        } else if (selectedVar === 'var2') {
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
                        Nome: entries["3"] + " " + entries["4"]
                      };
                    });
                let dataset;
                dataset = ids;
                provo(dataset);
                secondo(dataset)
              })
        }
        function secondo(data) {
          var dat = d3.nest()
              .key(function (d) {
                return d.Luogo;
              })
              .sortKeys(d3.ascending)
              .rollup(function (v) {
                return {
                  total: d3.sum(v, function (d) {
                    return d.Prezzo;
                  }),
                };
              })
              .entries(data);
          y1.domain(dat.map(function (d) {
            return d.key;
          }))
          //Bars*/
          x1.domain([0, 4000])
          xAxis1.transition().duration(1000).call(d3.axisBottom(x1))
          // Add Y axis
          yAxis1.transition().duration(1000).call(d3.axisLeft(y1));

          var u = svg2.selectAll("rect")
              .data(dat)

          u.exit()
              .transition()
              .duration(1000)
              .attr("width", 0)
              .remove();
          // update bars
          u.enter()
              .append("rect")
              .merge(u)
              .transition()
              .duration(1000)
              .attr("x", x1(0))
              .attr("y", function (d) {
                if (typeof y1(d.key) != 'undefined') {
                  return y1(d.key);
                }
              })
              .attr("width", function (d) {
                if (typeof y1(d.key) != 'undefined') {
                  return x1(d.value.total);
                }
              })
              .attr("height", y1.bandwidth())
              .attr("fill", RitornaColore)

          var c = svg2.selectAll(".text")
              .data(dat)
          c.exit()
              .transition()
              .duration(1000)
              //  .attr("width", 0)
              .remove();

          c.enter()
              .append("text")
              .merge(c)
              .attr('class', 'text')
              .style("fill", "black")
              .style("font-size", "9px")
              //  .attr("dy", ".35em")
              .attr("x", function (d) {
                if (typeof y1(d.key) != 'undefined') {
                  return x1(d.value.total)+10;
                }
              })
              .attr("y", function (d) {
                if (typeof y1(d.key) != 'undefined') {
                  return y1(d.key)+10 ;
                }
              })
              .style("style", "label")
              .text(function (d) {
                if (typeof y1(d.key) != 'undefined') {
                  return d.value.total;
                }
              });
        }
        function provo(data) {
          var dat = d3.nest()
              .key(function (d) {
                return d.Nome;
              })
              .sortKeys(d3.ascending)
              .rollup(function (v) {
                return {
                  total: d3.sum(v, function (d) {
                    return d.Prezzo;
                  }),
                };
              })
              .entries(data);
          y.domain(dat.map(function (d) {
            return d.key;
          }))
          //Bars*/
          x.domain([0, 4000])
          xAxis.transition().duration(1000).call(d3.axisBottom(x))
          // Add Y axis
          yAxis.transition().duration(1000).call(d3.axisLeft(y));

          var u = svg1.selectAll("rect")
              .data(dat)

          u.exit()
              .transition()
              .duration(1000)
              .attr("width", 0)
              .remove();
          // update bars
          u.enter()
              .append("rect")
              .merge(u)
              .transition()
              .duration(1000)
              .attr("x", x(0))
              .attr("y", function (d) {
                if (typeof y(d.key) != 'undefined') {
                  return y(d.key);
                }
              })
              .attr("width", function (d) {
                if (typeof y(d.key) != 'undefined') {
                  return x(d.value.total);
                }
              })
              .attr("height", y.bandwidth())
              .attr("fill", RitornaColore)
          var c = svg1.selectAll(".text")
              .data(dat)

          c.exit()
              .transition()
              .duration(1000)
            //  .attr("width", 0)
              .remove();

              c.enter()
               .append("text")
                  .merge(c)
                  .attr('class', 'text')
              .style("fill", "black")
              .style("font-size", "9px")
              //  .attr("dy", ".35em")
              .attr("x", function (d) {
                if (typeof y(d.key) != 'undefined') {
                  return x(d.value.total)+10;
                }
              })
              .attr("y", function (d) {
                if (typeof y(d.key) != 'undefined') {
                  return y(d.key)+10 ;
                }
              })
              .style("style", "label")
              .text(function (d) {
                if (typeof y(d.key) != 'undefined') {
                  return d.value.total;
                }
              });

          tabulate(dat, data, ['Employer con spesa oltre 2000$']); // 2 column table
      //    patterns(data);
        }
        function patterns(dataset,name) {
          var dat = d3.nest()
              .key(function (d) {
                return d.Nome;
              })
              .key(function (d) {
                return d.Luogo;
              })
              .rollup(function (v) {
                return {
                  total: d3.sum(v, function (d) {
                    return d.Prezzo;
                  }),
                };
              })
              .entries(dataset);
        var domain = []
          dat.map(function (d) {
            if (d.key===name){
              d.values.map(function(d){
                domain.push(d.key)
              });
            }
          })

          var Employer = []
          dat.map(function (d) {
            if (d.key===name){
              d.values.map(function(d){
                Employer.push(d)
              });
            }
          })
          y2.domain([0, d3.max(Employer, function(d) { return d.value.total })])
          //Bars*/
          x2.domain(domain)
          xAxis2.call(d3.axisBottom(x2))
          // Add Y axis
          yAxis2.transition().duration(1000).call(d3.axisLeft(y2));
          //PASSIAMO A u DEI DATI GIà IDENTIFICATIVI DI UN UNICO EMPLOYER
          var u = svg3.selectAll("rect")
              .data(Employer)

          u.exit()
              .transition()
              .duration(1000)
             // .attr("width", 0)
              .remove();
          // update bars
          u.enter()
              .append("rect")
           //   .merge(u)
              .transition()
              .duration(1000)
              .attr("y", 0)
              .attr("x", function (d) {
                      return x2(d.key)
                  })
             .attr("width", x2.bandwidth())
              .attr("height", function (d) {
            return height1 - d.value.total
          })
              .attr("fill", RitornaColore)

          var c = svg3.selectAll(".text")
              .data(Employer)

          c.exit()
              .transition()
              .duration(1000)
              //  .attr("width", 0)
              .remove();

          c.enter()
              .append("text")
              .merge(c)
              .attr('class', 'text')
              .style("fill", "black")
              .style("font-size", "9px")
              //  .attr("dy", ".35em")
              /* .attr("x", function (d) {
                 if (typeof y2(d.key) != 'undefined') {
                   return x2(d.value.total)+10;
                 }
               })
               .attr("y", function (d) {
                 if (typeof y2(d.key) != 'undefined') {
                   return y2(d.key)+10 ;
                 }
               })*/
              .attr("x", function (d) {
                    return x2(d.key)
                })
              .attr("y", function (d) {
                    return y2(d.value.total) + 10
              })
              .style("style", "label")
              /*  .text(function (d) {
                  if (typeof y2(d.key) != 'undefined') {
                    return d.value.total;
                  }
                });*/
              .text(function (d) {
                    return y2(d.value.total) -10
              });

        }
        function RitornaColore(d) {
          if (x(d.value.total > 2000)) {
            return "rgba(255,000,000, 0.3)";
          } else return "rgba(000,225,000, 0.3)";
        }
        function tabulate(data, data1, columns) {
          let newData = [];
          for (let i = 0; i < data.length; i++) {
            if (data[i].value.total > 2000) {
              newData.push(data[i])
            }
          }
          // create a row for each object in the data
          var rows = tbody.selectAll('tr')
              .data(newData)
          rows.exit()
             //  .transition()
             //  .duration(1000)
              //   .attr("width", 0)
              .remove();
          rows.enter()
              .append('tr')
              .merge(rows)
              .style('background-color', Color)
              .attr('class', "tab")
           //   .attr('id',fillId)

          //    .style('opacity', "0.6");
          // create a cell in each row for each column
          var cells = rows.selectAll('td')
              .data(function (row) {
                return columns.map(function (column) {
                  return {column: column, value: row.key + ":  " + row.value.total};
                });
              })

        cells.enter()
           .append('td')
             .merge(cells)
              // .style('background-color', Color)
              // .style('opacity', "0.6")
              .text(function (d) {
                return d.value;
              })
          rows.on('click', function(){
            var text = d3.select(this);
            var name= text.data()[0].key
            patterns(data1, name)
          })
      //  .on('click', patterns)
          function Color(d) {
            if (d.value.total > 2000) {
              return "rgba(255,000,000, 0.3)";
            }
          /*  function fillId(d){
              let B = d3.nest()
                  .key(function (d) {
                    return d.id
                  })
                  .entries(d);
              return B[0].key
            }*/
          }
        }
/*        function patterns(d){
          console.log(d)
         /!* var dat = d3.nest()
              .key(function (d) {
                return d.Nome;
              })
              .rollup(function (v) {
                return {
                  total: d3.sum(v, function (d) {
                    return d.Prezzo;
                  }),
                };
              })
              .key(function (d) {
                return d.Luogo;
              })
              .sortKeys(d3.ascending)
              .rollup(function (v) {
                return {
                  total: d3.sum(v, function (d) {
                    return d.Prezzo;
                  }),
                };
              })
              .entries(data);
          console.log(dat)*!/
         /!* let newData = [];
          for (let i = 0; i < dat.length; i++) {
            if (dat[i].value.total > 150 || dat[i].value.total) {
              newData.push(dat[i])
            }
          }
          console.log(newData)*!/
          var u = svg2.selectAll("rect")
              .data(d)
          u.append("rect")
        }*/
      }
    }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
#spese {
  width:68%;
  display: inline-block;
}
#appunti {
  width:32%;
  display: inline-block;
  float: right;
}
#luogo {
  width:50%;
  display: inline-block;
}
button:hover {
  background-color: #3e8e41;
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