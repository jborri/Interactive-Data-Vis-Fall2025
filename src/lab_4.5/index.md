---
title: "Lab 4: Clearwater Crisis"
toc: false
---

<!-- Import Data -->
```js
const bookcase = await FileAttachment("data/book_log.csv").csv({ typed: true });
```

```js
Inputs.table(bookcase)
```
```js
display(bookcase)
```

```js
Plot.plot({
    margin: 90,
  marks: [
    Plot.frame(),
    Plot.barX(bookcase, Plot.groupY(
      {x: "count"},
      {y: "genre_1",
        tip: true,
      },
      
    ))
    
  ]
})
```

<!-- Import Data -->
```js
const cognition = await FileAttachment("data/human_cognitive_performance.csv").csv({ typed: true });
```

```js
Inputs.table(cognition)
```
```js
display(cognition)
```
<style type="text/css">
            .bar {
                fill: steelblue;
            }
            .bar:hover {
                fill: skyblue;
            }
            .axis-x text {
                font-size: 12px;
            }
            .axis-y text {
                font-size: 12px;
            }

            #button1990s {
            background-color: blueviolet;
        }
        
            #button2000s {
            background-color: green;
        }
        
            #button2010s {
            background-color: blue;
        }
        
            #button2020s {
            background-color: goldenrod;
        }
        
            #button1990s:hover {
            background-color: mediumpurple;
        }
        
            #button2000s:hover {
            background-color: mediumseagreen;
        }
        
            #button2010s:hover {
            background-color: lightskyblue;
        }
        
            #button2020s:hover {
            background-color: yellow;
            
        }
        
</style>

<h1>Final Project: Analysis of Best Sellers List</h1>
        <button id="button1990s">1990s</button>
        <button id="button2000s">2000s</button>
        <button id="button2010s">2010s</button>
        <button id="button2020s">2020s</button>
        <br>

```js
const data = await FileAttachment("data/Yearly_Best_Sellers12 (1).csv").csv({ typed: true });
```
```js
  // d3.csv("data/Yearly_Best_Sellers12 (1).csv").then(data => {
  //           console.log("data", data)

        //Format Data
        data.forEach(d => {
                d.Genre = d.Genre;
                d.CountTotal = +d.CountTotal;
                d.Count90 = +d.Count90;
                d.Count00 = +d.Count00;
                d.Count10 = +d.Count10;
                d.Count20 = +d.Count20;
            });
```
```js
        const data1 = [
            { author: "John Grisham", books: 32 },
            { author: "Stephen King", books: 23 },
            { author: "Danielle Steel", books: 22 },
            { author: "James Patterson", books: 19 },
            { author: "Nicolas Sparks", books: 12 },
            { author: "Patricia Cornwell", books: 11 },
            { author: "Jeff Kinney", books: 11 },
            { author: "Tom Clancy", books: 10 },
            { author: "Janet Evanovich", books: 10 },
            { author: "Colleen Hoover", books: 10 }
        ];
```
```js
//BAR CONSTANTS
        const wbar = 600; // Width
        const hbar = 600; // Height
        const marginbar = 75; // Margin
        const paddingbar = 1; //padding

//SCATTER CONSTANTS

        const wdot = 600;
        const hdot = 600;
        const margindot = 75;

//PIE CONSTANTS
        const w = 700;
        const h = 600;
        const margin = 5;

            // Color constants
            const myColor = d3.scaleOrdinal(["lightgrey", "lightblue", "rgb(177, 226, 152)", "rgb(151, 151, 210)", "lightseagreen", "red", "orange", "yellow", "purple", "gold", "blue", "indigo", "Pink", "Green", "goldenrod"]);

            // Radius
            const outerRadius = 280;
            const innerRadius = 0;

            // Arc 
            const arc = d3.arc()
                .innerRadius(innerRadius)
                .outerRadius(outerRadius);

            // Pie generator for Total Count
            const pie = d3.pie().value(d => d.CountTotal);

            // Pie generator for 90s
            const pie90 = d3.pie().value(d => d.Count90);

            // Pie generator for 2000s
            const pie00 = d3.pie().value(d => d.Count00);

            // Pie generator for 2010s
            const pie10 = d3.pie().value(d => d.Count10);

            // Pie generator for 2020s
            const pie20 = d3.pie().value(d => d.Count20);

            // SVG
            const svg = d3.select("body")
                .append("svg")
                .attr("width", w)
                .attr("height", h);
```
```js
        //Graph Title
        svg.append("text")
                .attr("x", 80-(margin/2))
                .attr("y", 590)
                //.attr("font-size", "18px")
                //.attr("transform", "rotate(-90, 70, 300)")
                .attr("class", "title")
                .attr("fill", "rgb(116, 128, 110)")
                .text("Genres That Appear on Publishers Weekly Best Sellers by Decade");
```
```js
            // Draw Data Function
            function drawData(which) {
                svg.selectAll(".arc").remove(); // Remove existing pie chart arcs

                let pie;
                if (which === "Count90") pie = pie90;
                else if (which === "Count00") pie = pie00;
                else if (which === "Count10") pie = pie10;
                else if (which === "Count20") pie = pie20;
                else pie = pie20; // Default to Count20

                const groups = svg.selectAll("g.arc") // Select new arcs
                    .data(pie(data))
                    .enter()
                    .append("g")
                    .attr("class", "arc")
                    .attr("transform", "translate(" + outerRadius + "," + outerRadius + ")");

                groups.append("path")
                    .attr("fill", (d, i) => myColor(i))
                    .attr("d", arc);

               /* groups.append("text")
                    .attr("transform", d => "translate(" + arc.centroid(d) + ")")
                    .attr("text-anchor", "middle")
                    .attr("class", "labels")
                    .attr("fill", "black")
                    .text(d => d.data.Genre)
                    //.attr("transform", "rotate(-25)");*/
            }
```
```js
// SVG for Legend
const legend = svg.append("g")
    .attr("class", "legend")
    .attr("transform", "translate(600,50)"); // Adjust position?

// Add Legend Items
const legendItems = legend.selectAll(".legend-item")
    .data([...new Set(data.map(d => d.Genre))]) // Use unique genres
    .enter()
    .append("g")
    .attr("class", "legend-item")
    .attr("transform", (d, i) => "translate(0," + (i * 20) + ")"); // Adjust spacing
```
```js
// Add colored rectangles
legendItems.append("rect")
    .attr("x", 0)
    .attr("y", 0)
    .attr("width", 10)
    .attr("height", 10)
    .attr("fill", (d, i) => myColor(i)); // Use color scale to fill rectangles, i think lol

// Add genre labels
legendItems.append("text")
    .attr("x", 15)
    .attr("y", 5)
    .attr("dy", "0.35em")
    .text(d => d)
    .style("font-size", "10px")
    .attr("fill", "rgb(116, 128, 110)");
```

```js 
            //Legend Label
            
        svg.append("text")
                .attr("x", 600-(margin/2))
                .attr("y", 25)
                //.attr("font-size", "18px")
                //.attr("transform", "rotate(-90, 70, 300)")
                .attr("class", "title")
                .attr("fill", "Black")
                .text("Legend");

```
```js
            // Event listener for button clicks
            d3.selectAll("button").on("click", function () {
                const selectedText = d3.select(this).text(); // Get the text of the clicked button
                let dataType;
                if (selectedText === "1990s") {
                    dataType = "Count90";
                } else if (selectedText === "2000s") {
                    dataType = "Count00";
                } else if (selectedText === "2010s") {
                    dataType = "Count10";
                } else if (selectedText === "2020s") {
                    dataType = "Count20";
                }
                drawData(dataType); // Draw pie chart with this data type
            });
```
```js
            // Initial pie chart
            drawData("CountTotal");

            const xScale = d3.scaleLinear() 
                                //.domain([0, maxX])
                                .domain(d3.extent(data, d => d.Length))
                                .range([margin, 600]);
                                

            const yScale = d3.scaleLinear()
                               // .domain([0, maxY])
                                .domain([3, d3.max(data, d => d.Rating)])
                                .range([500-margin, margin]);
    
// Create x and y axes
const xAxis = d3.axisBottom()
                 .scale(xScale);
const yAxis = d3.axisLeft()
                .scale(yScale);

        // Append x axis to SVG
        svg.append("g")
            .attr("class", "axis")
            .attr("transform", "translate(55," + 1150 + ")")
            .call(xAxis);

        // Append y axis to SVG
        svg.append("g")
                .attr("class", "axis")
                .attr("transform", "translate(" + margin+5 + ",650)")
                //.attr("stroke", "purple")
                .call(yAxis);
            
```
```js
        //Scales

        const xScalebar = d3.scaleBand()
                        .domain(data1.map(d => d.author))
                        .range([marginbar, wbar - marginbar])
                        .paddingInner(paddingbar)
                        .padding(0.1);
                        

        const yScalebar = d3.scaleLinear()
                         .domain([0, d3.max(data1, d => d.books)+ 3])
                         .range([hbar - marginbar, marginbar]);

        //SVG element

        const svgbar = d3.select("body")
                      .append("svg")
                      .attr("id", "barchart") // Assigning an ID to the SVG element
                      .attr("width", wbar)
                      .attr("height", hbar)
                      .style("background-color", "rbg(153, 218, 204)");
                      //.attr("transform", "translate(800," + 0 + ")");

        //Axes

        const bottomAxisbar = d3.axisBottom()
                             .scale(xScalebar);

        svgbar.append("g")
           .attr("class", "axis")
           .attr("transform", "translate(0," + (hbar - marginbar) + ")")
           .call(bottomAxisbar)
           .selectAll("text")
            .style("text-anchor", "end")
            .attr("dx", "-.8em")
            .attr("dy", ".15em")
            .attr("transform", "rotate(-25)");

        const leftAxisbar = d3.axisLeft()
                           .scale(yScalebar);

        svgbar.append("g")
            .attr("class", "axis")
            .attr("transform", "translate(" + marginbar + ", 0)")
            .call(leftAxisbar);

        //Bars
        svgbar.selectAll(".bar")
           .data(data1)
           .enter()
           .append("rect")
           .attr("class", "bar")
           .attr("x", d => xScalebar(d.author))
           .attr("y", d => yScalebar(d.books))
           .attr("width", xScalebar.bandwidth())
           .attr("height", d => (hbar-marginbar) - yScalebar(d.books));

        //Chart Labels
    //x axis labels
    svgbar.append("text")
                .attr("x", 300-(marginbar/2))
                .attr("y", 595)
                .attr("font-size", "18px")
                .attr("fill", "black")
                .text("Authors");

            //y axis labels
            svgbar.append("text")
                .attr("x", 0)
                .attr("y", 300-(marginbar/2))
                .attr("font-size", "18px")
                .attr("transform", "rotate(-90, 75, 300)")
                .attr("fill", "black")
                .text("Number of Books");

            //Graph Title
            svgbar.append("text")
                .attr("x", 150-(marginbar/2))
                .attr("y", 40)
                .attr("font-size", "18px")
                //.attr("transform", "rotate(-90, 70, 300)")
                .attr("fill", "black")
                .text("Top 10 Authors by Appearance on Best Sellers List");

```
```js
//SCATTER

        // Constants (same values as pie)



        // Data (using same data as pie)



        const maxX = d3.max(data, d => d.Length);
        const maxY = d3.max(data, d => d.Rating);

        console.log("data", data);

        // SVG
        
         const svgdot = d3.select("body")
                       .append("svg")
                       .attr("width", wdot)
                       .attr("height", hdot);
```
```js
    //Axes
    const xScaledot = d3.scaleLinear() 
                                //.domain([0, maxX])
                        .domain(d3.extent(data, d => d.Length))
                        .range([margindot, wdot-margindot]);
                                

    const yScaledot = d3.scaleLinear()
                               // .domain([0, maxY])
                        .domain([3, d3.max(data, d => d.Rating)])
                        .range([hdot-margindot, margindot]);

    //bottom Axis
    const bottomAxisdot = d3.axisBottom()
                            .scale(xScaledot)
                            .ticks(10);

            svgdot.append("g")
                  .attr("transform", "translate(0," + (hdot - margindot) + ")")
                  .call(bottomAxisdot);

    //Left Axis
    const leftAxisdot = d3.axisLeft()
                          .scale(yScaledot)
                          .ticks(10);

        svgdot.append("g")
              .attr("transform", "translate(" + margindot + ", 0)")
              .call(leftAxisdot);

```
```js
//Circles
        svgdot.selectAll("circle")
            .data(data)
            .enter()
            .append("circle")
            .attr("cx", d => xScaledot(d.Length))
            .attr("cy", d => yScaledot(d.Rating))
            .attr("r", 3) // radius of circles
            //.attr("transform", "translate(60," + 650 + ")")
            .attr("fill", "red");
    
            svgdot.append("text")
                .attr("x", 300-(margindot/2))
                .attr("y", 595)
                .attr("font-size", "18px")
                .attr("fill", "black")
                .text("Number of Pages");

            //y axis labels
            svgdot.append("text")
                .attr("x", 0)
                .attr("y", 300-(margindot/2))
                .attr("font-size", "18px")
                .attr("transform", "rotate(-90, 75, 300)")
                .attr("fill", "black")
                .text("Rating");

            svgdot.append("text")
                .attr("x", 280-(margindot/2))
                .attr("y", 50)
                .attr("font-size", "18px")
                //.attr("transform", "rotate(-90, 70, 300)")
                .attr("class", "title")
                .attr("fill", "rgb(116, 128, 110)")
                .attr("fill", "black")
                .text("Rating vs. Book Length");
        ;
    
```

