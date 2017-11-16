---
layout: default
title: Switching To Programmer's DVORAK
---

# Switching To Programmer's DVORAK

I have long been fascinated with alternatives to the standard QWERTY layout. I was aware that other keyboard layouts existed and was vaguely familiar with [Dvorak](http://www.wikiwand.com/en/Dvorak_Simplified_Keyboard) and [Colemak](http://www.wikiwand.com/en/Keyboard_layout#/Colemak). I was aware that the alternatives offered speed improvements once mastered, but this was not a concern as the majority of my typing is writing code and short communications. I finally decided to make the switch once I started reading about the health benefits of using a layout that had more of the common letters on the home row and the subsequent reduced finger strain. As I spend a large amount of my day typing, I am always on the lookout for workplace ergonomic improvements.

After deciding to switch layouts, I decided I would do some research into what else was out there. The search ended once I found the [Programmer's Dvorak "sub"-layout](http://www.kaufmann.no/roland/dvorak/). It combines the typing layout in DVORAK - most frequent letters on home row and alternating hands while typing words, with the reworking of various symbols to be more programmer-friendly. The layout can be seen below:

![Programmer's Dvorak](/assets/images/programmers-dvorak-layout.png)
*http://programmer-dvorak.appspot.com/img/KB_Programmer_Dvorak.png*

# Progress

I started learning the new layout through the very helpful lessons listed [here](http://programmer-dvorak.appspot.com/). They slowly introduced new letters while maintaining the muscle memory of the previous ones. I simply went through each lesson a few times for a half hour each night and started to notice improvement. While learning, I took frequent tests of my typing speed on [Ratatype](http://ratatype.com/typing-test/test/) and recorded the results in the graphs at the end of this post. My biggest mistake during this period was missing a few weeks of lessons during an extended vacation in August. This can be seen with the sharp leveling off of my speed improvements. Once returning, I had to go back a few lessons and re-learn the keys that weren't already committed to muscle memory.

After a few months, my speed grew increasing tolerable and I could start using the new layout for typing outside of the lessons. Windows provides a shortcut to quickly switch layouts (Win + space) if I got stuck or frustrated, but this became less of a necessity as my skills improved. My DVORAK speed is currently about half my QWERTY speed at 42WPM and I am able to spend long periods of time exclusively in DVORAK both for typing English words and programming in C#.

# Maintaining QWERTY

One concern I had during my learning was that I would lose my proficiency at QWERTY. I could work exclusively in DVORAK at my personal and work machines, but I still had to interact with my phone and other's computers. I decided that once proficient in DVORAK, I would still switch back every once in a while to maintain both layouts. I am further ensuring this by tracking my QWERTY speed as well in the graphs below.

# Lessons Learned

* Tracking the progress can be a major motivator, especially when the speed difference with the new layout leads to frustration.
* Be consistent, especially until you have a general idea of where all the keys are, before skipping lessons.
<!-- * Switching can boa done slowly or quickly as long as you are consistent. I only spent 30 minutes a day and was able to retrain my fingers. -->
* You can be fluent in multiple layouts as long as you consistently use both.
* There is a period of a few minutes once familiar with two layouts while you slowly remember the correct muscle memory for the current layout. You can still perform proficiently in both, but it is sometimes temporarily confusing quickly switching between them.


The graphs will be updated over the next few months while my DVORAK speed catches up with QWERTY.

# Final Update

After tracking my progress for the last two years, I have decided to stop tracking it. I have committed the DVORAK layout to muscle memory and can switch between the two seemlessly. I use DVORAK about 80% of the time while working, but have no issue switching mid-task between layouts. The charts indicate that I have not fully matched QWERTY speed, but I believe this to be because I have not used DVORAK sufficiently to type long-winded documents; most of my work involves only typing in short bursts for chat messages or the like. I enjoy using DVORAK as my primary layout because I can feel the difference in the general finger movement between the two layouts.


<figure class="wpm">  
  <figcaption>Words Per Minute (WPM) improvement</figcaption>
</figure>

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
  color: #cb4b16;
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
