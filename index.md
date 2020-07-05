

<meta charset="utf-8">
<style>

body{
background-color: #F5F5DC;
}
#DatViz{
  position: relative;
}
h2{
  position: relative;
  text-align: center;
}
#red{
  height: 12px;
  font-size: 100px;
  line-height: 12px;
  color: red;
}
#blue{
  font-size: 100px;
  color: blue;
  line-height: 12px;
}
#green{
  font-size: 100px;
  color: green;
  line-height: 12px;
}
p{
  vertical-align: text-top;
}

#signs{
  position: fixed;
  top: 300px;
  right: 100px;
}

path {
    stroke: black;
    stroke-width: 2;
    fill: none;
}

.axis path,
.axis line {
    fill: none;
    stroke: "red";
    stroke-width: 1;
    shape-rendering: crispEdges;
}

div.tooltip {
    position: absolute;
    text-align: center;
    width: 40px;
    height: 15px;
    padding: 2px;
    font: 12px sans-serif;
    background: #C37B77;
    border: 0px;
    pointer-events: none;
}

</style>
<body>
  <h2>Aktívan szolgálatot teljesítő amerikai katonák száma Németországban</h2>
  <div id="DatViz"></div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.17/d3.min.js"></script>

<script>

var margin = {top: 30, right: 30, bottom: 30, left: 300},
    width = 1300 - margin.left - margin.right,
    height = 200 - margin.top - margin.bottom;
var pDate = d3.time.format("%Y").parse;
var ft = d3.time.format("%Y");
var x = d3.time.scale().range([0, width]);
var y = d3.scale.linear().range([height, 0]);

var xAxis = d3.svg.axis().scale(x)
    .orient("bottom").ticks(10);
var yAxis = d3.svg.axis().scale(y)
    .orient("left").ticks(5);

var theline = d3.svg.line()
    .x(function(d) { return x(d.date); })
    .y(function(d) { return y(d.active); });

var div = d3.select("body").append("div")
    .attr("class", "tooltip")
    .style("opacity", 0);

var svg = d3.select("body")
    .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
    .append("g")
        .attr("transform",
              "translate(" + margin.left + "," + margin.top + ")");


d3.csv("https://raw.githubusercontent.com/mohosm/RFE_stuff/master/germany_deployment.csv", function(error, data) {
    data.forEach(function(d) {
        d.date = pDate(d.Year);
        d.active = +d.Active;
    });
    x.domain(d3.extent(data, function(d) { return d.date; }));
    y.domain([0, d3.max(data, function(d) { return d.active; })]);



    svg.append("path")
        .attr("class", "line")
        .attr("d", theline(data));

    svg.selectAll("dot")
        .data(data)
    .enter().append("circle")
        .attr("r", 4)
        .attr("cx", function(d) { return x(d.date); })
        .attr("cy", function(d) { return y(d.active); })
        .on("mouseover", function(d) {
            div.transition()
                .duration(200)
                .style("opacity", .9);
            div	.html(d.active)
                .style("left", (d3.event.pageX) + "px")
                .style("top", (d3.event.pageY - 28) + "px");
            })
        .on("mouseout", function(d) {
            div.transition()
                .duration(500)
                .style("opacity", 0);
        });
        svg.append("g")
            .attr("class", "x axis")
            .attr("transform", "translate(0," + height + ")")
            .call(xAxis);
        svg.append("g")
            .attr("class", "y axis")
            .call(yAxis);

});

</script>
</body>
