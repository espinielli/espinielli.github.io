---
layout: post
title: How to add a new chart type to dc.js
date: '2015-04-21 17:56:42'
author: enrico_spinielli
comments: true
tags:
- dc.js
- d3.js
- dataviz
- bullet-chart
- javascript
modified_time: '2015-04-26 14:38:55'
---

This is a step by step description of how I extended dc.js with a new chart type. It is inspired by a [wiki page](https://github.com/loganalysis/analytics/wiki/Adding-a-new-chart) by [Thomas Robert](http://www.thomas-robert.fr/en/).

## A Bullet chart ##
[Mike Bostock](http://bost.ocks.org/mike/) already implemented a [bullet chart](http://bl.ocks.org/mbostock/4061961) in [D3.js](http://d3js.org). The [vertical version](http://bl.ocks.org/jasondavies/5452290) from [Jason Davies](http://www.jasondavies.com/) uses the official code which is available as a [d3-plugin](https://github.com/d3/d3-plugins/tree/master/bullet). (I have submitted a [proposal for a bug fix](https://github.com/d3/d3-plugins/issues/130) and will use it instead.)

So how can I make it available in [dc.js](http://dc-js.github.io/dc.js/)?

<br>
<br>
Below you can find the description of what I did and what is still in the todo list.

The result is not too bad:


<iframe src="https://cdn.rawgit.com/espinielli/e7625ce617e4d9c87cae/raw/af63bae2ab20491d80006e6616d1dbde4747b1d5/index.html" marginwidth="0" marginheight="0" scrolling="no" width="200px"></iframe>
<p><aside><a style="position:relative;top:6px;" href="http://bl.ocks.org/espinielli/raw/e7625ce617e4d9c87cae/" target="_blank">Open in a new window.</a></aside>
<br>

## Scaffolding ##

Starting from `dc.js` directory
 
* add a file named `src\bullet-chart.js`
* copy `bullet.js` (with the fix proposed [here](https://github.com/d3/d3-plugins/issues/130)) from the [d3-plugin](https://github.com/d3/d3-plugins/tree/master/bullet) to in `src\d3.bullet.js`
* add the two files above in the `module.exports.jsFiles` array in `Gruntfile.js`

```javascript
	module.exports.jsFiles = [
		...
		'src/d3.bullet.js',
		'src/bullet-chart.js',
		'src/footer.js'  // NOTE: keep this last
	];
```

* add the example file `examples\web\bullet.html`, a copy of `ord.html` for example, and update `examples\web\index.html` accordingly


## Stub your chart ##

`bullet-chart.js` can initially be something like


```javascript
	/**
	## Bullet Chart
	Includes: [Margin Mixin](#margin-mixin), [Color Mixin](#color-mixin),
	          [Base Mixin](#base-mixin)
	
	Bullet chart implementation.
	#### dc.bulletChart(parent[, chartGroup])
	Create a bullet chart instance and attach it to the given parent element.
	
	#### Example of usage
	
	```javascript
	var chart = dc.bulletCloudChart()
	 .dimension(department)
	 .group(salesPerDepartment)
	 .colors(d3.scale.quantize().range(["#E2F2FF", "#C4E4FF", "#9ED2FF", 
	 "#81C5FF", "#6BBAFF", "#51AEFF",  "#36A2FF", "#1E96FF", "#0089FF", "#0061B5"]))
	 .colorDomain([0, 200])
	 .label(function (d) { return labels[d.key]; })
	 .title(function (d) { return d.value+" $"; })
	```
	
	
	Parameters:
	
	* parent : string | node | selection - any valid
	[d3 single selector](https://github.com/mbostock/d3/wiki/Selections#selecting-elements)
	specifying a dom block element such as a div; or a dom element or d3 selection.
	
	* chartGroup : string (optional) - name of the chart group this chart instance should
	be placed in.
	Interaction with a chart will only trigger events and redraws within the chart's group.
	
	Returns:
	A newly created bullet chart instance
	
	```javascript
	// create a bullet chart under #chart-container1 element using the default global chart group
	var chart1 = dc.bulletChart('#chart-container1');
	// create a bullet chart under #chart-container2 element using chart group A
	var chart2 = dc.bulletChart('#chart-container2', 'chartGroupA');
	```
	
	**/
	dc.bulletChart = function (parent, chartGroup) {
	
		var _chart = dc.marginMixin(dc.colorMixin(dc.baseMixin({}))));
	
		//--- specifics ---
	
		//-----------------
	
	 return _chart.anchor(parent, chartGroup);
	};
```

The rationale for the mixin used is:
* Base: you cannot really get away from it
* Margin: useful to get proper spacing around
* Color: allows for selecting the bullet colors

The rendering for the new chart is to be coded in the `//--- specifics ---` part.

The source code (yes! It should be better documented, hence this post) of Base Mixin specifies that `_doRender` and `_doRedraw` are the functions to be implemented in the concrete charts.

## Rendering the chart ##
Luckily for bullet charts there are examples to get inspired from both for the [horizontal](http://bl.ocks.org/mbostock/4061961) and the [vertical](http://bl.ocks.org/jasondavies/5452290) layout.

The `_doRender` function is mimicking what done by [Mike Bostock](http://bost.ocks.org/mike/) in his horizontal layout with the parametrization of the title `transform`.

```javascript
  var _bulletMargin = {top: 5, right: 40, bottom: 20, left:120},
      _bulletWidth = 960 - _bulletMargin.left - _bulletMargin.right,
      _bulletHeight = 50 - _bulletMargin.top  - _bulletMargin.bottom,
      _bulletOrient = "left",
      _titleTranslate = titleTranslate(_bulletOrient);


  _chart._doRender = function () {
    var _bullet = d3.bullet()
      .width(_bulletWidth)
      .height(_bulletHeight)
      .orient(_bulletOrient);

    var svg = _chart.root().selectAll("svg")
        .data(_chart.data())
      .enter().append("svg")
        .attr("class", "bullet")
        .attr("width", _bulletWidth + _bulletMargin.left + _bulletMargin.right)
        .attr("height", _bulletHeight + _bulletMargin.top  + _bulletMargin.bottom)
      .append("g")
        .attr("transform", "translate(" + _bulletMargin.left + "," + _bulletMargin.top + ")")
        .call(_bullet);


    var title = svg.append("g")
        .style("text-anchor", "end")
        .attr("transform", "translate(" + _titleTranslate[0] + "," + _titleTranslate[1] + ")");

    title.append("text")
        .attr("class", "title")
        .text(function(d) {
          return d.title;
        })

    title.append("text")
        .attr("class", "subtitle")
        .attr("dy", "1em")
        .text(function(d) {
          return d.subtitle;
        })

    return _chart;
  };
```

The proper positioning of the title is taken care by the `titleTranslate` function. Titles will either be on the left of the bullet bar in the horizontal layout or at the bottom in the vertical one.

## Chart options ##
The bullet chart can be customized in order to produce the desired graph via the getter/setter methods described in the following sections.

These are quite low level customizations, see the [To Do](#todo) section for a better approach.

###  .bulletWidth ###

```javascript
  /**
  #### .bulletWidth([value])
  Set or get the bullet width.

  **/
  _chart.bulletWidth = function (_) {
      if (!arguments.length) {
          return _bulletWidth;
      }
      _bulletWidth = +_;
      return _chart;
  };
```

### .bulletHeight ###

```javascript
  /**
  #### .bulletHeight([value])
  Set or get the bullet height.

  **/
  _chart.bulletHeight = function (_) {
      if (!arguments.length) {
          return _bulletHeight;
      }
      _bulletHeight = +_;
      return _chart;
  };
```

### .bulletMargin ###
```javascript
  /**
  #### .bulletMargin([value])
  Set or get the bullet margin, i.e. `{top: 5, right: 40, bottom: 50, left:120}`.

  **/
  _chart.bulletMargin = function (_) {
      if (!arguments.length) {
          return _bulletMargin;
      }
      _bulletMargin = _;
      return _chart;
  };
```

### .orient ###
This method defines the starting point for the bullet.

Note that it influences where the title/subtitle will be positioned: the current implementation of `bullet.js` allows for title to either be on the left or at the bottom in the horizontal and vertical layout respectively.

The internal function `titleTranslate` sets sensible values for the title position.

```javascript
  /**
  #### .orient([value])
  Set or get the bullet orientation (one of `"left"`, `"right"`, `"top"` or `"bottom"`).

  **/
  _chart.orient = function (_) {
      if (!arguments.length) {
          return _bulletOrient;
      }
      _bulletOrient = _;
      _titleTranslate = titleTranslate(_bulletOrient);
      return _chart;
  };
```

### titleTranslate (internal) ###
This internal function sets the right parameters for the positioning of the title/subtitle for the vertical and horizontal layout.

```javascript
  function titleTranslate(orient) {
    if (!arguments.length) {
      return _titleTranslate;
    }
    
    if (_bulletOrient == "left" || _bulletOrient == "right") {
      return [-6, _bulletHeight / 2];
    }
    else if (_bulletOrient == "bottom" || _bulletOrient == "top") {
      return [_bulletWidth, _bulletHeight + 20]
    }

    return [-6, _bulletHeight / 2];
  }
```

## Example ##
The `bullet.html` file is structured pretty much the same as the other examples:

* the head part, 
* the libraries, 
* the style part
* the `<div>`'s for the charts
* the code for the chart instantiation and rendering

```html
<!DOCTYPE html>
<html lang="en">
<head>
<title>dc.js - Bullet Chart Example</title>
	<meta charset="UTF-8">
	<link rel="stylesheet" type="text/css" href="../css/dc.css"/>
</head>
<script type="text/javascript" src="../js/d3.js"></script>
<script type="text/javascript" src="../js/crossfilter.js"></script>
<script type="text/javascript" src="../js/dc.js"></script>

<style>
  /* see "A Matter of Style" section */
</style>


<div id="test-horizontal"></div>
<div id="test-vertical"></div>

<script type="text/javascript">
  // see "The Two Layouts" section
</script>
</html>
```

### The Two Layouts ###
The example mimics charts in the gists from Mike Bostock and Jason Davies.

There is the usual binding to the `<div>`'s, the crossfilter bits and the chart definition and rendering.

Note the trick about how `statusGroup` has been defined in order to comply with `dc.js` way to pass the data to the underlying `d3.js`: this is based on the knowledge that the default implementation of `.data()` is returning `group.all()`.

```javascript
var chart1 = dc.bulletChart("#test-horizontal");
var chart2 = dc.bulletChart("#test-vertical");

var data = [
  {"title":"Revenue","subtitle":"US$, in thousands","ranges":[150,225,300],"measures":[220,270],"markers":[250]},
  {"title":"Profit","subtitle":"%","ranges":[20,25,30],"measures":[21,23],"markers":[26]},
  {"title":"Order Size","subtitle":"US$, average","ranges":[350,500,600],"measures":[100,320],"markers":[550]},
  {"title":"New Customers","subtitle":"count","ranges":[1400,2000,2500],"measures":[1000,1650],"markers":[2100]},
  {"title":"Satisfaction","subtitle":"out of 5","ranges":[3.5,4.25,5],"measures":[3.2,4.7],"markers":[4.4]}
];

var ndx        = crossfilter(data),
    titleDimension = ndx.dimension(function(d) {return d.title;}),
    statusGroup    = {
      all: function(){
        return data;
    }};

// dims from Mike Bostock's bl.ock, http://bl.ocks.org/mbostock/4061961
chart1
  .width(960)
  .height(450)
  .bulletMargin({top: 5, right: 40, bottom: 20, left: 120})
  .bulletWidth(960 - 120 - 40)
  .bulletHeight(50 - 5 - 20)
  .orient("left")
  .dimension(titleDimension)
  .group(statusGroup);

chart1.render();

// dims from Jason Davies's bl.ock, http://bl.ocks.org/jasondavies/5452290
chart2
  .width(185)
  .height(450)
  .bulletMargin({top: 5, right: 40, bottom: 50, left: 120})
  .bulletWidth(185 - 120 - 40)
  .bulletHeight(450 - 5 - 50)
  .orient("top")
  .dimension(titleDimension)
  .group(statusGroup);

chart2.render();
```


### A Matter of Style ###
In the best tradition of the web, everything in the chart can be customized via CSS

```css
body {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  margin: auto;
  padding-top: 40px;
  position: relative;
  width: 960px;
}

button {
  position: absolute;
  right: 10px;
  top: 10px;
}

.bullet { font: 10px sans-serif; }
.bullet .marker { stroke: #000; stroke-width: 2px; }
.bullet .tick line { stroke: #666; stroke-width: .5px;}
.bullet .range.s0 { fill: #eee; }
.bullet .range.s1 { fill: #ddd; }
.bullet .range.s2 { fill: #ccc; }
.bullet .measure.s0 { fill: lightsteelblue; }
.bullet .measure.s1 { fill: steelblue; }
.bullet .title { font-size: 14px; font-weight: bold; }
.bullet .subtitle { fill: #999; }

.bullet .axis line, .bullet .axis path { opacity: 0.5; }
</style>

```

## <a name="todo"></a> To Do ##
The current implementation lacks few feature and refinements.

### Tests ###
I am studying the existing ones and will add them!

### Automatic Layout ###

From a user perspective I would like to just say:

```javascript
  chart
    .width(960)
    .height(450)
    .orient("left")
    .dimension(titleDimension)
    .group(statusGroup);
```
  
and have sensible width/height/margins being calculated by the internals of the implementation, with the above methods available for fine-tuning.

### Colors selection ###
Even if I made `bulletChart` include the [Color Mixin](https://github.com/dc-js/dc.js/blob/master/web/docs/api-latest.md#color-mixin), I haven't really tackled colors customization.

With code like the following

```javascript
  chart
    .width(960)
    .height(450)
    .orient("left")
    .dimension(titleDimension)
    .group(statusGroup)
    .colors(d3.scale.ordinal().range(['red','green','blue']));
```

you would be able to define the three colors for the `bad`, `satisfactory` and `good` ranges.


###  Chart Margins  ###
Even if included, the [Margin Mixin](https://github.com/dc-js/dc.js/blob/master/web/docs/api-latest.md#margin-mixin) hasn't been handled.

These will be the margins for the whole chart, not the ones for the bullets as described above.

<style>
iframe {
  width: 660px;
  height: 500px;
  border: 1px solid #DEDEDE;
}

aside {
  font-size: small;
  left: 780px;
  position: absolute;
  text-align: right;
  width: 180px;
  color: #636363;
}
 aside a {
    text-decoration: underline;
 }
</style>
