# W12 Lab Assignment

# Part 1:
See the ipynb file.

# Part 2: Maps II

## Bubble maps

We'll display the 10 fastest growing cities in the US, with bubble sizes representing their populations. This is mostly putting things we've already covered together. 

Start from this skeleton from the last lab :

    <!DOCTYPE html>
    <meta charset="utf-8">
    <style>

    .counties {
 		fill: #000;
 			stroke: #fff;
 			stroke-width: 0.25px;
    }

    .states {
 			fill: none;
 			stroke: #fff;
 			stroke-linejoin: round;
    }
    </style>

    <body>
    <script src="http://d3js.org/d3.v3.min.js"></script>
    <script src="http://d3js.org/topojson.v1.min.js"></script>
    <script>

    var width = 960,
   			height = 500;

    var projection = d3.geo.albersUsa()
				  		   	  .scale(1000)
				  		       .translate([width / 2, height / 2]);

    var path = d3.geo.path()
        		 		  .projection(projection);

    var svg = d3.select("body").append("svg")
					 .attr("width", width)
					 .attr("height", height);

    d3.json("us.json", function(error, us) {

				svg.append("g")
  	   			   .attr("class", "counties")
  	   			   .selectAll("path")
  	   			   .data(topojson.feature(us, us.objects.counties).features)
  	   			   .enter().append("path")
  	   			   .attr("d", path);

				svg.append("path")
   	   			   .datum(topojson.mesh(us, us.objects.states, function(a, b) { return                a !== b; }))
   	   			   .attr("class", "states")
   	   			   .attr("d", path);

    });
    </script>
    </body>
    
We can make it a little more nice-looking. 

**TODO**: Change the color of the map from black to #fcfa86 and color the state and county boundaries in orange.

The data we'll use is `city_growth.csv` (on Canvas). You'll also need `us.json`. To read in the data:

    d3.csv("growth.csv", function(error, data) {
		// Drawing of map already done before
		// Now we will draw the points here
    });

#### Drawing circles

Recall that drawing a circle requires two parameters - the center (`cx`, `cy`) and the radius (`r`). Add the following within the d3.csv() function.

    svg.selectAll("circle")
  	   .data(data)
  	   .enter()
  	   .append("circle")
  	   .attr("cx", function(d) {return projection([d.longitude, d.latitude])[0];})
  	   .attr("cy", function(d) {return projection([d.longitude, d.latitude])[1];})
  	   .attr("r", 5)
  	   .style("fill", "red");

The main trick here is that we are applying the Albers projection to the longitudinal and latitudinal information in the CSV file. The output of this application serves as the center for each point.

Changing the size and transparency of the circles:

For the radius, we can use a hardcoded scaling function. Change the correspoding code as the following:

    svg.selectAll("circle")
  	   .data(data)
  	   .enter()
  	   .append("circle")
  	   .attr("cx", function(d) {return projection([d.longitude, d.latitude])[0];})
  	   .attr("cy", function(d) {return projection([d.longitude, d.latitude])[1];})
  	   .attr("r", function(d) {return Math.sqrt(d.population * 0.00004);})
  	   .style("fill", "red");

For the opacity, we can just set a number between 0 and 1.

**TODO**: Set the transparency of the circles by setting the `opacity`.

### Adding text labels

Finally, we want to place the text labels such that there are placed close enough to the points that we have drawn.

We could keep them right at the center of each circle, i.e. the x and y co-ordinates of the text would correspond to the cx and cy values of the circles. But that would not be clean enough.

We could move them to the right by a bit. But by how much? If we need to prevent them from overlapping with the circles, we need to move by a distance equal to the radius at the least. Now, the x-value will be:
			                 
cx + radius + some offset

**TODO**: Add the labels. Update the CSS style in the header to make all the labels colored red, with font-family sans-serif and font-size 10px. 

**Submit this file on Canvas.** An example of what the map would look like:

![image](https://github.com/yy/dviz-course/blob/master/w12-geo-2/map_example.png)
