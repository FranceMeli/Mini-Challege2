<template>
  <div id="T-timeline">
    <div id="selezionato">Hai selezionato l'utente con id: {{checkedNames[0]}}</div>
    <div id="timeline6"></div>
  </div>
</template>

<script>

const d3 = require("d3");
//const topojson = require("topojson")
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
    var labelColorTestData = [
      {label: "person a", times: [{"color":"green", "label":"Weeee", "starting_time": 1355752800000, "ending_time": 1355759900000}, {"color":"blue", "label":"Weeee", "starting_time": 1355767900000, "ending_time": 1355774400000}]},
      {label: "person b", times: [{"color":"pink", "label":"Weeee", "starting_time": 1355759910000, "ending_time": 1355761900000}, ]},
      {label: "person c", times: [{"color":"yellow", "label":"Weeee", "starting_time": 1355761910000, "ending_time": 1355763910000}]}
    ];

    var width = 500;
    let svg = d3.select("#timeline6").append("svg").attr("width", width)
    function timelineLabelColor() {
      var chart = d3.timeline()
          .beginning(1355752800000) // we can optionally add beginning and ending times to speed up rendering a little
          .ending(1355774400000)
          .stack() // toggles graph stacking
          .margin({left:70, right:30, top:0, bottom:0})
      ;
       svg
           .datum(labelColorTestData).call(chart);
    }


    timelineLabelColor();
}}


</script>