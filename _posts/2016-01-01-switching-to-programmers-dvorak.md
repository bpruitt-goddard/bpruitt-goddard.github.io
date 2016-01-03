---
layout: default
---


Words per minute improvement graph below!


<figure class="wpm">
  <figcaption>Words Per Minute (WPM) improvement</figcaption>
</figure>

And here is the one for accuracy!

<figure class="accuracy">
  <figcaption>Accuracy improvement</figcaption>
</figure>

<style>
path { 
    stroke: #cb4b16;
    stroke-width: 2;
    fill: none;
}

.axis path,
.axis line {
    fill: none;
    stroke: grey;
    stroke-width: 1;
    shape-rendering: crispEdges;
}

.axis text, .axis-label {
  fill: #839496;
}

.legend {
    font-size: 16px;
    font-weight: bold;
    text-anchor: middle;
}

figcaption {
  text-align: center;
  font-size: 1.5em;
}

</style>
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
<script type="text/javascript">

//D3 design inspired by http://www.d3noob.org/2014/07/d3js-multi-line-graph-with-automatic.html

var parseDate = d3.time.format("%Y/%m/%e").parse;
var margin = {top: 30, right: 20, bottom: 70, left: 50},
    width = 600 - margin.left - margin.right,
    height = 300 - margin.top - margin.bottom;
// Set the ranges
var x = d3.time.scale().range([0, width]);
var y = d3.scale.linear().range([height, 0]);

// Define the axes
var xAxis = d3.svg.axis().scale(x)
    .orient("bottom").ticks(5).tickFormat(d3.time.format("%b %y"));

var yAxis = d3.svg.axis().scale(y)
    .orient("left").ticks(5);

function CreateWPMGraph() {
    // Define the line
  var wpmline = d3.svg.line() 
      .x(function(d) { return x(d.date); })
      .y(function(d) { return y(d.wpm); });
      
  var svg = CreateSVGCanvas("figure.wpm");

  // Get the data
  d3.csv("/assets/data/keyboardWPM.csv", function(error, data) {
      data.forEach(function(d) {
        d.date = parseDate(d.date);
        d.wpm = +d.wpm;
      });

      // Scale the range of the data
      x.domain(d3.extent(data, function(d) { return d.date; }));
      y.domain([0, d3.max(data, function(d) { return d.wpm; })]);

      SeparateDataByLayoutKey(svg, data, wpmline);

      CreateXAxis(svg);
      CreateYAxisWithLabel(svg, "words per minute (wpm)");
  });
}

function CreateAccuracyGraph() {

  // Define the line
  var accuracyline = d3.svg.line() 
      .x(function(d) { return x(d.date); })
      .y(function(d) { return y(d.accuracy); });
      
  var svg = CreateSVGCanvas("figure.accuracy");

  // Get the data
  d3.csv("/assets/data/keyboardAccuracy.csv", function(error, data) {
    data.forEach(function(d) {
      d.date = parseDate(d.date);
      d.accuracy = +d.accuracy;
    });

    // Scale the range of the data
    x.domain(d3.extent(data, function(d) { return d.date; }));
    y.domain([50, 100]);

    SeparateDataByLayoutKey(svg, data, accuracyline);

    CreateXAxis(svg);
    CreateYAxisWithLabel(svg, "accuracy");
  });
}

function CreateXAxis(svg) {
  svg.append("g")
      .attr("class", "x axis")
      .attr("transform", "translate(0," + height + ")")
      .call(xAxis);
}

function CreateYAxisWithLabel(svg, labelText) {
  // Add the Y Axis
  svg.append("g")
      .attr("class", "y axis")
      .call(yAxis);

  svg.append("text")
    .attr("class", "y axis-label")
    .attr("text-anchor", "middle")
    .attr("y", 6)
    .attr("dy", "-3em")
    .attr("dx", "-8em")
    .attr("transform", "rotate(-90)")
    .text(labelText);
}

function CreateSVGCanvas(elementToAppendToSelector) {
  return d3.select(elementToAppendToSelector)
        .append("svg")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
        .append("g")
            .attr("transform", 
                  "translate(" + margin.left + "," + margin.top + ")")
}

function SeparateDataByLayoutKey(svg, data, lineFunction) {
  // Nest the entries by symbol
  var dataNest = d3.nest()
      .key(function(d) {return d.layout;})
      .entries(data);

  var color = d3.scale.category10();  // set the colour scale

  // Loop through each symbol / key
  dataNest.forEach(function(d) {
      svg.append("path")
          .attr("class", "line")
          .style("stroke", function() { // Add dynamically
              return d.color = color(d.key); })
          .attr("d", lineFunction(d.values));
  });

  legendSpace = width/dataNest.length; // spacing for legend

  // Loop through each symbol / key
  dataNest.forEach(function(d,i) {
      svg.append("path")
          .attr("class", "line")
          .style("stroke", function() { // Add the colours dynamically
              return d.color = color(d.key); })
          .attr("d", lineFunction(d.values));

      // Add the Legend
      svg.append("text")
          .attr("x", (legendSpace/2)+i*legendSpace) // spacing
          .attr("y", height + (margin.bottom/2)+ 5)
          .attr("class", "legend")    // style the legend
          .style("fill", function() { // dynamic colours
              return d.color = color(d.key); })
          .text(d.key);
  });
}

CreateWPMGraph();
CreateAccuracyGraph();

</script>