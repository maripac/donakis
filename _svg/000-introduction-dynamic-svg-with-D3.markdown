---
layout: svgInSeries
title: "000 Introduction Dynamic SVG with D3"
comments: true
date: 2014-11-29 19:21:16
categories: 
index: 000
short-name: "Introduction SVG with D3"
cabecera: "000 Introduction SVG and D3"
published: true
---
<div class="inner2" style="width:100%; margin-top:1.125em; border:1px solid #ccc; overflow-x:scroll;">

<script>
var svg = d3.select(".inner2").append("svg").attr({
	width: 838,
	height:360

});


var dataset = [
					[
	                  [ 85,  50 ],
	                  [ 20,  165 ],
	                  [ -45,  50 ]
	                ],
	                [
	                  [ 100,  50 ],
	                  [ 165,  165 ],
	                  [ 35,  165 ]
	                ]

			];



var polygons = svg.selectAll("polygon")
			.data(d3.range(6)).enter()
			.append("polygon");

polygons.attr({
		points: function (d, i){
			return (dataset[0][0][0] + (i * 160)) + "," + (dataset[0][0][1]) + " " + (dataset[0][1][0] + (i * 160)) + "," + (dataset[0][1][1]) + " " + (dataset[0][2][0] + (i * 160)) + "," + (dataset[0][2][1]);
		},
		// fill: function (d){
		// 	return "rgb(50, 150, " + (d * 50) + ")";
		// }
		fill: "#2fcab2"
	})
	.style({
		display: function(d){
			if(d == 2 || d == 0 || d == 5){
				return "none";
			}
		}
	});

var moreData = d3.range(11);

var poly002 = svg.selectAll("polygon").data(moreData);

poly002.enter().append("polygon")
			.attr({
	points: function (d, i){
		return (dataset[1][0][0] + ((i - 6) * 160)) + "," + (dataset[1][0][1]) + " " + (dataset[1][1][0] + ((i - 6) * 160)) + "," + (dataset[1][1][1]) + " " + (dataset[1][2][0] + ((i - 6) * 160)) + "," + (dataset[1][2][1]);
	},
	fill: "#205098"
});



var moreData001 = d3.range(16);

var poly003 = svg.selectAll("polygon").data(moreData001);

poly003.enter().append("polygon")
			.attr({
	points: function (d, i){
		return (dataset[1][0][0] + ((i - 11) * 160)) + "," + (dataset[1][0][1] + 240) + " " + (dataset[1][1][0] + ((i - 11) * 160)) + "," + (dataset[1][1][1] + 10) + " " + (dataset[1][2][0] + ((i - 11) * 160)) + "," + (dataset[1][2][1] + 10);
	},
		// fill: function (d){
		// 	return "rgb(50, 150, " + (d * 50) + ")";
		// }
		fill: "#2fcab2"
});

var moreData002 = d3.range(22);

var poly004 = svg.selectAll("polygon").data(moreData002);

poly004.enter().append("polygon")
			.attr({
	points: function (d, i){
		return (dataset[0][0][0] + ((i - 16) * 160)) + "," + (dataset[0][0][1] + 240) + " " + (dataset[0][1][0] + ((i - 16) * 160)) + "," + (dataset[0][1][1] + 10) + " " + (dataset[0][2][0] + ((i - 16) * 160)) + "," + (dataset[0][2][1] + 240);
	},
	fill: "transparent",
	stroke: "#2fcab2",
	"stroke-width": "4px"
	
	})
	.style({
		display: function(d){
			if(d == 16 || d == 21){
				return "none";
			}
		}
	});





</script>


</div>



A polygon is a svg element that defines a graphic shape delimited by at least three sides. In order to
generate a polygon we must provide an array containing the values needed to locate in an x and y coordinate system, each corner of the polygon. Each corner, therefore takes an array of two values, one for the x coordinate, and another for the y coordinate. These array of values must be provided inside the attribute "points" as in the following syntax:

{% highlight html %}
<polygon points="135,5 70,115 5,5" style="fill:#F95837;" />
{% endhighlight %}

The result of the previous example would look like the followin graphic:

<svg height="140" width="500">
	<polygon points="135,5 70,115 5,5" style="fill:#F95837;" />
</svg>


D3.js is a javascript library that allowes the generation of elements in the DOM, binded to data input values. These elements are generated dynamically in the client which means that any update of the DOM tree, will also be processed by the client, avoiding the need of submitting requests to the server, which adds considerable amounts of latency and increases the amount of data being transfered during the connection by forcing new page reloads.

In order, to generate the previous polygon dynamically with D3.js we must bind some data to the polygon element, that will be needed when defining the polygon's attributes. Reviewing the previous: we have said that a polygon is a svg element that takes an atrribute "points" which must contain an array of at least three values separated from each other by a space, each of these three values takes an array of two values, both of them provide the position of a corner of the polygon in a x and y coordinate system. The first value provides the coordinate that sets the position of the corner in the x axis, and separated from the first by a comma, the second value provides the coordinate that sets the position of the corner in the y axis. 

We could provide those coordinates using the following dataset: 
 

{% highlight js %}
var dataset = [
	[
		[ 135,   5 ],
		[ 70,  115 ],
		[ 5,     5 ]
	]
];
{% endhighlight %}

So the final D3.js script would look like this:


{% highlight js linenos=table %}


var svg = d3.select("body").append("svg").attr({
	width: 838,
	height:160
});

var dataset = [
	[
		[ 135,   5 ],
		[ 70,  115 ],
		[ 5,     5 ]
	]
];

var polygon = svg.selectAll("polygon")
	.data(dataset).enter()
	.append("polygon");

polygon.attr({
	points: function(d){ return d; },
	fill: "#F95837"
});
{% endhighlight %}

Checkout the code in line 19. 

<div class="inner3" style="width:100%; margin-top:1.125em; border:1px solid #ccc; overflow-x:scroll;">

<script>
var svg = d3.select(".inner3").append("svg").attr({
	width: 838,
	height:360

});


var dataset = [
	[
		[ 135,   5 ],
		[ 70,  115 ],
		[ 5,     5 ]
	]

];

var polygon = svg.selectAll("polygon")
			.data(dataset).enter()
			.append("polygon");

polygon.attr({
	//points: function(d){ return d[0] + " " + d[1] + " " + d[2]; },
	points: function(d){ return d; },
	fill: "#F95837"
});

var text = svg.selectAll("text")
			.data(dataset).enter()
			.append("text");

text.attr({
	//points: function(d){ return d[0] + " " + d[1] + " " + d[2]; },
	//text: function(d){ return d; },
	fill: "#333333",
	x: 10,
	y: 160,
	"font-size": "24px",
	"font-family": "sans-serif"
	})
	.text(function(d) { return "A/ points: function(d){ return d; }  =>   " + d ; });

var text2 = svg.selectAll("g").append("g")
			.data(dataset).enter()
			.append("text");

text2.attr({
	//points: function(d){ return d[0] + " " + d[1] + " " + d[2]; },
	//text: function(d){ return d; },
	fill: "#333333",
	x: 10,
	y: 210,
	"font-size": "24px",
	"font-family": "sans-serif"
	})
	.text(function(d) { return "B/ points: function(d){ return d[0] + ' ' + d[1] + ' ' + d[2]; }   =>  " + d[0] + " " + d[1] + " " + d[2]; });


</script>

</div>





<div class="inner" style="width:100%; margin-top:1.125em; border:1px solid #ccc; overflow-x:scroll;">

<script>
var svg = d3.select(".inner").append("svg").attr({
	width: 838,
	height:360

});


var dataset = [
					[
	                  [ 135,  5 ],
	                  [ 70,  115 ],
	                  [ 5,  5 ]
	                ],
	                [
	                  [ 150,  5 ],
	                  [ 205,  115 ],
	                  [ 85,  5 ]
	                ],
	                [
	                  [ 100,  50 ],
	                  [ 165,  165 ],
	                  [ 35,  165 ]
	                ]

			];



var polygons = svg.selectAll("polygon")
			.data(d3.range(6)).enter()
			.append("polygon");

polygons.attr({
		points: function (d, i){
			return (dataset[0][0][0] + (i * 160)) + "," + (dataset[0][0][1]) + " " + (dataset[0][1][0] + (i * 160)) + "," + (dataset[0][1][1]) + " " + (dataset[0][2][0] + (i * 160)) + "," + (dataset[0][2][1]);
		},
		// fill: function (d){
		// 	return "rgb(50, 150, " + (d * 50) + ")";
		// }
		fill: "#2fcab2"
	})
	.style({
		display: function(d){
			if(d == 2 || d == 0 || d == 5){
				return "none";
			}
		}
	});

var moreData = d3.range(11);

var poly002 = svg.selectAll("polygon").data(moreData);

poly002.enter().append("polygon")
			.attr({
	points: function (d, i){
		return (dataset[1][0][0] + ((i - 6) * 160)) + "," + (dataset[1][0][1]) + " " + (dataset[1][1][0] + ((i - 6) * 160)) + "," + (dataset[1][1][1]) + " " + (dataset[1][2][0] + ((i - 6) * 160)) + "," + (dataset[1][2][1]);
	},
	fill: "#205098"
});



var moreData001 = d3.range(16);

var poly003 = svg.selectAll("polygon").data(moreData001);

poly003.enter().append("polygon")
			.attr({
	points: function (d, i){
		return (dataset[1][0][0] + ((i - 11) * 160)) + "," + (dataset[1][0][1] + 240) + " " + (dataset[1][1][0] + ((i - 11) * 160)) + "," + (dataset[1][1][1] + 10) + " " + (dataset[1][2][0] + ((i - 11) * 160)) + "," + (dataset[1][2][1] + 10);
	},
		// fill: function (d){
		// 	return "rgb(50, 150, " + (d * 50) + ")";
		// }
		fill: "#2fcab2"
});

var moreData002 = d3.range(22);

var poly004 = svg.selectAll("polygon").data(moreData002);

poly004.enter().append("polygon")
			.attr({
	points: function (d, i){
		return (dataset[0][0][0] + ((i - 16) * 160)) + "," + (dataset[0][0][1] + 240) + " " + (dataset[0][1][0] + ((i - 16) * 160)) + "," + (dataset[0][1][1] + 10) + " " + (dataset[0][2][0] + ((i - 16) * 160)) + "," + (dataset[0][2][1] + 240);
	},
	fill: "#eaedf7",
	stroke: "#2fcab2",
	"stroke-width": "0px"
	
	})
	.style({
		display: function(d){
			if(d == 16 || d == 21){
				return "none";
			}
		}
	});


</script>
</div>






