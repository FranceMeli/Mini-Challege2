<template>
  <div id="T-map">
    <div id="selezionato">Hai selezionato l'utente con id: {{checkedNames[0]}}</div>
    <div id="Map"></div>
    <div>
    <label>
      Seleziona una data compresa tra il 06/01/2014 e il 19/01/2014:
      <input id="calendar" type="date" name="data" min="2014-01-06" max="2014-01-19" step="1" required>
      <span class="validity"></span>
    </label>
    </div>
    <div id="vis">
    <button id="b1"  type="button" class="btn btn-info">Play</button>
    </div>
  </div>
</template>

<script>
const d3 = require("d3");
const topojson = require("topojson")

export default {
  name: 'MC2Vehicles',
  data() {
    return {
      persons: [],
      p1: [],
      p2: [],
      counter: 0,
    };
  },
  props: ['checkedNames'],
  mounted: function () {
    console.log(this.checkedNames)
    var width = 900;
    var height = 600;
    var inputGiorno = null;
    let features;
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
    var margin = {top:50, right:50, bottom:0, left:50},
        width1 = 800,
        height1 = 250;
    var svg1 = d3.select("#vis")
        .append("svg")
        .attr("width", width1 + margin.left + margin.right)
        .attr("height", height1 + margin.top + margin.bottom);
    var moving = false;
    var timer=null;
    var currentValue = 0;

    var targetValue = width1;
    var playButton = d3.select("#b1");
    var x = d3.scaleLinear() //d3.scaleTime()
        .domain([0, 86400])
        .range([0, targetValue])
        .clamp(true);

    var slider = svg1.append("g")
        .attr("class", "slider")
        .attr("transform", "translate(" + margin.left + "," + height1/5 + ")");
    slider.append("line")
        .attr("class", "track")
        .attr("x1", x.range()[0])
        .attr("x2", x.range()[1])
        .select(function() { return this.parentNode.appendChild(this.cloneNode(true)); })
        .attr("class", "track-inset")
        .select(function() { return this.parentNode.appendChild(this.cloneNode(true)); })
        .attr("class", "track-overlay")
    /*  .call(d3.drag()
          .on("start.interrupt", function() { slider.interrupt(); })
          .on("start drag", function(evt,value) {
            var pos = d3.select('#slider').text(value[ 0 ]);
            currentValue = pos;
            handle.attr("cx", x(currentValue));
        //    update(x.invert(currentValue));
          })
      );*/
    var handle = slider.insert("circle", ".track-overlay")
        .attr("class", "handle")
        .attr("r", 9);

    var label = slider.append("text")
        .attr("class", "label")
        .attr("text-anchor", "middle")
        .text("A")
        .attr("transform", "translate(0," + (-25) + ")")
    let time=0
    let id;
    d3.select("#calendar").on("input", function () {
      inputGiorno = this.value
    });
    d3.select("#button").on("click", function() {
      var boxes = d3.selectAll("input.checkbox:checked");
      boxes.each(function() {
        id=this.id
      })
    });
    let gg;
    playButton
        .on("click", function() {
          console.log(id)
          let ID=id
          let giorno= inputGiorno.slice(5,7) + "-" + inputGiorno.slice(8,10) + "-" + inputGiorno.slice(0,4)
          gg= inputGiorno.slice(5,7) + "/" + inputGiorno.slice(8,10) + "/" + inputGiorno.slice(0,4)
          let riprova = []
          let dataId=[]
          let trajs=[]
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
                        time: entries["0"],
                        times: entries["0"],
                      };
                    });
                let dataset = [];
                dataset = ids;
                for (let i = 0; i < dataset.length; i = i + 3) {
                  if (ID!= undefined) {
                    if (dataset[i].id == ID) {
                      let secondi = trasformaOra(dataset[i].Timestamp.slice(11,));
                      dataset[i].times = secondi
                      dataset[i]["Timestamp"] = dataset[i].Timestamp.slice(0, 10)
                      dataset[i]["time"] = dataset[i].time.slice(11,)
                      dataId.push(dataset[i])
                    }
                  }
                  else {
                    let secondi = trasformaOra(dataset[i].Timestamp.slice(11,));
                    dataset[i].times = secondi
                    dataset[i]["Timestamp"] = dataset[i].Timestamp.slice(0, 10)
                    dataset[i]["time"] = dataset[i].time.slice(11,)
                    riprova.push(dataset[i])
                  }
                }
                if(ID!=undefined){
                  trajs = d3.nest()
                      .key(d => d.id)
                      .entries(dataId);
                }
                else {
                  trajs = d3.nest()
                      .key(d => d.id)
                      .entries(riprova);
                }
                /*  playButton
                      .on("click", function () {*/
                var button = d3.select(this);
                if (button.text() == "Play") {
                  moving = true;
                  // timer = setInterval(step, 100);
                  timer = d3.interval(step);
                  button.text("Pause");
                }
                else if (button.text() == "Pause") {
                  moving = false;
                  timer.stop()
                  // clearInterval(timer);
                  // timer = 0;
                  button.text("Play");
                }
                currentValue= FirstHour(trajs)
                var t = LastHour(trajs)
                targetValue = t*860/86400
                console.log(trajs)
                console.log("Slider moving: " + moving);
              })
          function step() {
            time=currentValue*860/86400
            if (time > targetValue) {
              moving = false;
              currentValue = FirstHour(trajs);
              timer.stop()
              //  clearInterval(timer);
              // timer = 0;
              playButton.text("Play");
              console.log("Slider moving: " + moving);
            }else {
              update(currentValue,trajs)   //(x.invert(currentValue));
              currentValue = currentValue + 1;
              //(targetValue);
            }
          }
        })
    function LastHour(da){
      var MaxTime=[]
      for (let i=0; i<da.length;i++){
        MaxTime.push(da[i].values[(da[i].values.length - 1)].times)
      }
      var max = d3.max(MaxTime)
      return max;
    }
    function FirstHour(da){
      var MinTime=[]
      for (let i=0; i<da.length;i++){
        MinTime.push(da[i].values[0].times)
      }
      var min = d3.min(MinTime)
      return min
    }

    function update(h,d) {
      /*var myColor = d3.scaleLinear().domain([0,35])
           //  .range(["red", gold", "blue", "green", "yellow", "black", "grey", "darkgreen", "pink", "brown", "slateblue", "grey1", "orange"])
            .range(["red", "black"])*/
      svg
          .selectAll("circle")
          .data(d.map(function(d) {
            return d.values}))
          .enter()
          .append("circle")
          .attr("class", "move")
          .attr("r", 5)
          .attr("id", fillId)
          .attr("fill", colora)

      d3.selectAll(".mov")
          .attr('id', function(d){
            return d.Id
          })
          .style('background-color',coloraTab)

      d3.selectAll(".move")
          .attr("cx", CxTime)
          .attr("cy", CyTime)
      // update position and text of label according to slider scale
      handle.attr("cx", x(h));
      label
          .attr("x", x(h))
          .text(TrasformaSecondi(h))
    }
    function fillId(d){
      let B = d3.nest()
          .key(function (d) {
            return d.id
          })
          .entries(d);
      return B[0].key
    }
    function colora(d){
      let B = d3.nest()
          .key(function (d) {
            return d.id
          })
          .entries(d);
      let ID = B[0].key
      let color;
      if (ID > 35) {
        color = color = "rgb(" + (ID) + "," + (2 * ID) + "," + (3 * ID) + ")";
      } else color = "rgb(" + (2 * ID) + "," + (3 * ID) + "," + (4 * ID) + ")";
      return color
    }
    function coloraTab(d){
      let color;
      let ID = d.Id
      if (ID < 37) {
        color = "rgb(" + (255 / ID) + "," + (4 * ID) + "," + (6 * ID) + ")";
      } else color = "rgb(" + (ID) + "," + (2 * ID) + "," + (3 * ID) + ")";
      return color
    }
    function CxTime(d) {
      let lati=false
      let longi=false
      let B = d3.nest()
          .key(function (d) {
            if (d.Timestamp == gg) {
              return d.Timestamp
            }
          }).sortKeys(d3.ascending)
          .entries(d);
      if (B[0].key == gg) {
        let lo = B[0].values.map(function (d) {
          if (time == x(d.times)) {
            longi = true;
            let long = d.long
            return long
          }
        })
        let la = B[0].values
            .map(function (d) {
              //  this.parentElement.appendChild(this);
              //  let a=d.times*860/86400
              if (time == x(d.times)) {
                lati = true;
                let lat = d.lat
                return lat
              }
            })
        la.sort(d3.ascending)
        lo.sort(d3.ascending)
        if (lati & longi) {
          return projection([lo[0], la[0]])[0];
        }
      }}
    function CyTime(d) {
      let lati=false
      let longi=false
      let B = d3.nest()
          .key(function (d) {
            if (d.Timestamp == gg) {
              return d.Timestamp
            }
          }).sortKeys(d3.ascending)
          .entries(d);
      if (B[0].key == gg) {
        let lo = B[0].values.map(function (d) {
          //  this.parentElement.appendChild(this);
          if (time == x(d.times)) {
            longi = true;
            let long = d.long
            return long
          }
        })
        let la = B[0].values.map(function (d) {
          //  this.parentElement.appendChild(this);
          if (time == x(d.times)) {
            lati = true;
            let lat = d.lat
            return lat
          }
        })
        la.sort(d3.ascending)
        lo.sort(d3.ascending)
        if (lati & longi) {
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
    function TrasformaSecondi(h){
      let  hours = Math.floor(h/ 3600);
      let  totalSeconds = h % 3600;
      let  minutes = Math.floor(totalSeconds / 60);
      let  seconds = totalSeconds % 60;

      let tot = hours + ":" + minutes + ":" + seconds;
      return tot

    }
  }

}

</script>