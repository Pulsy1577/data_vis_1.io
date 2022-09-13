<!-- Code from d3-graph-gallery.com -->
<!DOCTYPE html>
<meta charset="utf-8">
<html>


<style>
    body {
        background-color: WhiteSmoke;
    }
    .grid line {
        stroke: lightgrey;
        stroke-opacity: 0.7;
        shape-rendering: crispEdges;
    }
</style>
<body>

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<h1 style="text-align: center; font-family:verdana;"> Hypothesis: Latitudes further from the equator will have more frequent earthquakes </h1>
<hr>
<p style="text-align: center; font-family:verdana;"> The data is a set of the first 1000 recorded earthquake events(not including mining etc.) of 2022 over magnitude 2.5 in the contiguous United States</p>
<p style="text-align: center; font-family:verdana; font-size: 12px"> All data is taken from the United States Geological Service (USGS) website https://www.usgs.gov/</p>

<div style="padding-bottom: 435px">
    <div style="float:left; padding-left: 100px" id="my_dataviz" ></div>
    <div style="float:right; padding-right: 100px" id="my_dataviz2"></div>
</div>

<p style="text-align: center; font-family:verdana;"> In the end I do not believe my hypothesis was confirmed, however I was surprised by how many events occurred between 30 and 35 degrees, I wonder why this was?</p>
<p style="text-align: center; font-family:verdana;"> A few things to consider: This data is not representative of the whole world, a better visualization would have global data from many years back. The reason for the limiting of the data set was that working with a set that large was not the introduction to d3 I wanted</p>

<script>
    /*
    HYPO: LAttitudes further from the equator will have more higher magnitude earthquakes
    first 1000 recorded earthquake events over magnitude 2.5 in 2022 in the contiguous US. USGS
    */
    //HISTOGRAM
    // set the dimensions and margins of the graph

    var margin = {top: 25, right: 30, bottom: 45, left: 60},
        width = 600 - margin.left - margin.right,
        height = 430 - margin.top - margin.bottom;

    // append the svg object to the body of the page
    var chart1 = d3.select("#my_dataviz2")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform",
            "translate(" + margin.left + "," + margin.top + ")");

    // get the data
    d3.csv("https://raw.githubusercontent.com/Pulsy1577/Data-Visualization-Project-1/main/lat_only.csv", function(data) {

        // X axis: scale and draw:
        var x = d3.scaleLinear()
            .domain([20, 55])     // can use this instead of 1000 to have the max of data: d3.max(data, function(d) { return +d.price })
            .range([0, width]);
        chart1.append("g")
            .attr("transform", `translate(0, ${height})`)
            .call(d3.axisBottom(x));
        chart1.append("text")
            .attr("transform",
                "translate(" + (width/2) + " ," +
                (height - 8 + margin.top + 20) + ")")
            .style("text-anchor", "middle")
            .text("Latitude(degrees)");


        // set the parameters for the histogram
        var histogram = d3.histogram()
            .value(function (d) {
                return d.Latitude;
            })   // I need to give the vector of value
            .domain(x.domain())  // then the domain of the graphic
            .thresholds(x.ticks(8)); // then the numbers of bins

        // And apply this function to data to get the bins
        var bins = histogram(data);

        // Y axis: scale and draw:
        var y = d3.scaleLinear()
            .range([height, 0]);
        y.domain([0, d3.max(bins, function (d) {
            return d.length;
        })]);   // d3.hist has to be called before the Y axis obviously
        chart1.append("g")
            .call(d3.axisLeft(y));
        chart1.append("text")
            .attr("transform", "rotate(-90)")
            .attr("y", 0 - margin.left)
            .attr("x",0 - (height / 2))
            .attr("dy", "1em")
            .style("text-anchor", "middle")
            .text("Number of events");
        chart1.append("text")
            .attr("x", (width/2))
            .attr("dy", "-10px")
            .style("text-anchor", "middle")
            .style("font-size", "18px")
            .text("# of Events per Latitude");

        // append the bar rectangles to the svg element
        chart1.selectAll("rect")
            .data(bins)
            .enter()
            .append("rect")
            .attr("x", 1)
            .attr("transform", function (d) {
                return "translate(" + x(d.x0) + "," + y(d.length) + ")";
            })
            .attr("width", function (d) {
                return x(d.x1) - x(d.x0) - 1;
            })
            .attr("height", function (d) {
                return height - y(d.length);
            })
            .style("fill", "DarkCyan")

    })

        //============================================================================
        //SCATTER
    // set the dimensions and margins of the graph
    var margin = {top: 25, right: 30, bottom: 45, left: 60},
        width = 600 - margin.left - margin.right,
        height = 430 - margin.top - margin.bottom;

    // append the svg object to the body of the page
    var svg = d3.select("#my_dataviz")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform",
            "translate(" + margin.left + "," + margin.top + ")");

    //Read the data
    d3.csv("https://raw.githubusercontent.com/Pulsy1577/Data-Visualization-Project-1/main/quake_data2.csv", function(data) {

        // Add X axis
        var x = d3.scaleLinear()
            .domain([20, 55])
            .range([ 0, width ]);
        svg.append("g")
            .attr("transform", `translate(0, ${height})`)
            .call(d3.axisBottom(x));
        svg.append("text")
            .attr("transform",
                "translate(" + (width/2) + " ," +
                (height - 8 + margin.top + 20) + ")")
            .style("text-anchor", "middle")
            .text("Latitude(degrees)");

        // Add Y axis
        var y = d3.scaleLinear()
            .domain([2.5, 5.5])
            .range([ height, 0]);
        svg.append("g")
            .call(d3.axisLeft(y).ticks(8));
        svg.append("text")
            .attr("transform", "rotate(-90)")
            .attr("y", 0 - margin.left)
            .attr("x",0 - (height / 2))
            .attr("dy", "1em")
            .style("text-anchor", "middle")
            .text("Magnitude");
        svg.append("text")
            .attr("x", width/2)
            .attr("dy", "-10px")
            .style("text-anchor", "middle")
            .style("font-size", "18px")
            .text("Magnitude v Latitude");

        function make_x_gridlines() {
            return d3.axisBottom(x)
                .ticks(5)
        }

        function make_y_gridlines() {
            return d3.axisLeft(y)
                .ticks(5)
        }
        //Gridliness
        svg.append("g")
            .attr("class", "grid")
            .attr("transform", "translate(0," + height + ")")
            .call(make_x_gridlines()
                .tickSize(-height)
                .tickFormat("")
            )

        svg.append("g")
            .attr("class", "grid")
            .call(make_y_gridlines()
                .tickSize(-width)
                .tickFormat("")
            )



        // Add dots
        svg.append('g')
            .selectAll("dot")
            .data(data)
            .enter()
            .append("circle")
            .attr("cx", function (d) { return x(d.Latitude); } )
            .attr("cy", function (d) { return y(d.magnitude); } )
            .attr("r", 1.5)
            .style("fill", "DarkCyan")

    })

</script>
</body>
</html>
