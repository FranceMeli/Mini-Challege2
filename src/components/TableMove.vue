<template>
  <div id="T-table">
    <b-col>
    <div id="selezionato">Hai selezionato l'utente con id: {{checkedNames[0]}}</div>
      <p :value="Employer">Clicca luogo, giorno e ora delle transazione dell' employer {{checkedNames[0]}}, {{Employer}} </p>
      <button id="button">Vedi</button>
<!--      <div id="tab">
        <p> L'employer <b>{{Employer}}</b> si è spostato nei seguenti luoghi nei giorni compresi tra il 1/6/2014 e il 1/19/2014</p>
      </div>-->
      <div v-for="pers in Luoghi" :key="pers.Employer" class="pcol">
        <label>Il giorno {{pers["key"]}}: </label>
          <div v-for="v in pers.values" :key="v.Time">
          <label>{{v.Luogo}}</label> <label v-if="v.Time!==''"> alle ore {{v.Time}}, pagamento con credit card</label>
            <label v-if="v.Time===''"> (non sappiamo l'orario), pagamento con loyalty card </label>
            </div>
      </div>

    </b-col>
  </div>
</template>

<script>

const d3 = require("d3");

export default {
  name: 'MC2Vehicles',
  props: ['checkedNames'],
  data() {
    return {
      data: "",
      Luoghi: [],
      Ore: "",
      Employer: "",
    };
  },
  mounted: function () {
    d3.select("#button").on("click", function () {
      var boxes = d3.selectAll("input.checkbox:checked");
      boxes.each(function () {
        this.Employer = this.value
      })
      d3.csv("https://raw.githubusercontent.com/FranceMeli/Mini-Challege2/master/static/Alltransaction1.csv")
          .then((rows) => {
            const ids = rows
                .map((row) => {
                  const entries = d3.values(row)
                  //console.log(entries["0"].length)
                  return {
                    Time: entries["0"].slice(10,),
                    Day: entries["0"].slice(0, 9),
                    Luogo: entries["1"],
                    Employer: entries["3"] + " " + entries["4"]
                  };
                });
            this.Luoghi=ids
          })
    })
        let Data= this.Luoghi
        let boxe = d3.select("input.checkbox:checked");
        console.log(boxe)
        let E = boxe._groups[0][0].Employer
        let tab= []
        for (let i = 0; i < Data.length; i = i + 5) {
          if (tab[i].Employer === E) {
            tab.push(tab[i])
          }
        }
        var columns = ['1/6/2014', '1/7/2014', '1/8/2014', '1/9/2014', '1/10/2014', '1/11/2014',
          '1/12/2014', '1/13/2014', '1/14/2014', '1/15/2014', '1/16/2014',
          '1/17/2014', '1/18/2014', '1/19/2014'];
        let ragr = d3.nest()
            //  .key(d => d.Employer)
            .key(function (d) {
              return d.Day;
            }).sortKeys(function (a, b) {
              return columns.indexOf(a) - columns.indexOf(b);
            })
            .entries(tab);
        this.Luoghi=ragr;
  },
  methods: {
}
  }


   /* function tabulate(data, columns) {
      var table = d3.select('#persons')
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
          .attr("class", "mov")
      //    .style('background-color',)
      //    .style('opacity', "0.6");

      // create a cell in each row for each column
      rows.selectAll('td')
          .data(function (row) {
            console.log(row)
            return columns.map(function (column) {

              return {column: column, value: row.values[0].Luogo};
            });
          })
          .enter()
          .append('td')
          // .style('background-color', Color)
          // .style('opacity', "0.6")
          .text(function (d) { return d.value; });
      return table;
    } */

</script>

<style>
</style>