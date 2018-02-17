# Practical SVG

THIS IS A WORK IN PROGRESS 

## Why write this?

I have never been a fan of SVG, preferring HTML canvas over SVG for performance and simplicity. With growing support of the `CanvasRenderingContext2D.filter` property which uses SVG filters and fills a much needed hole in the Canvas API I find my self writing more and more SVG related code.

As a programmer I am inherently lazy, computers are to serve me and make life simpler, Yet SVG is a oiled pig requiring too much effort to get the bacon.

## What does Practical SVG do?

It simplifies the process of creating low level SVG DOM elements providing a transparent method of setting attributes, and appending nodes. It also transforms property values and names between XML and JavaScript thus greatly reducing the complexity of creating SVG content on the fly.

PracticalSVG turns...

```Javascript
const width = 100;
const height = 100;
const resizeBy = 10;
const pathPoints = [[0,0], [100,0], [100,100], [0,100], [50,50], [0,0]];
const svgNode = document.createElementNS("http://www.w3.org/2000/svg", "svg");
svgNode.setAttribute("width", width + "px");
svgNode.setAttribute("height", height+ "px");
const path = document.createElementNS("http://www.w3.org/2000/svg", "path");
parentNode.appendChild(path);
path.setAttribute("points", pathPoints.map(point => point.join(",").join(" "));
document.body.appendChild(svgNode);
svgNode.setAttribute("width", (Number(svgNode.getAttribute("width").replace("px","")) + (resizeBy * 2)) + "px");
svgNode.setAttribute("height", (Number(svgNode.getAttribute("height").replace("px","")) + (resizeBy * 2)) + "px");
const points = path.getAttribute("points").split(" ").map(point => point.split(" ").map(coord => Number(coord)));
points.forEach(point => { point[0] += 10; point[1] += 10;})
path.setAttribute("points", points.map(point=>point.join(",").join(" "));
```

into this...

```Javascript
const width = 100;
const height = 100;
const resizeBy = 10;
const pathPoints = [[0,0], [100,0], [100,100], [0,100], [50,50], [0,0]];
const svg = createSVG("svg", {width : width, height : height});
svg.foo = createSVG("path", {points : pathPoints });
document.body.appendChild(svg.node);
svg.width  += resizeBy;
svg.height += resizeBy;
svg.foo.points = svg.foo.points.map(point => (point[0] += 10, point[1] += 10, point));
```


The resulting SVG is identical, but the amount of effort as a programmer is greatly reduced



## Using Practical SVG

Add the Javascript file to the page.

```Javascript
<script src="createSVG.js"></script>
```

To create a simple SVG node.

```Javascript
const svg = createSVG("svg"); // the node name
svg.width = 300;
svg.height = 150;
svg.style = "border : 2px solid black;"; // add a style

// Add a rectangle
svg.rect1 = createSVG("rect");
svg.rect1.x = 10;
svg.rect1.y = 10;
svg.rect1.width = 280;
svg.rect1.height = 130;
svg.rect1.style = "fill : red; stroke: black; stroke-width: 3";

// Or pass the attribute when you create the node
svg.rect1 = createSVG("rect", {x : 10, y : 10, width : 20, height: 20, fill="green"});

// Create a blur filter
svg.filter = createSVG("filter");  
svg.filter.id = "blur";
svg.filter.feBlur = createSVG("feGaussianBlur");  
svg.filter.feBlur.in = "SourceGraphic";  
svg.filter.feBlur.stdDeviation = 5;  

// add a circle
svg.circle1 = createSVG("circle");
svg.circle1.cx = 150;
svg.circle1.cy = 75;
svg.circle1.r = 50;
svg.circle1.fill = "yellow";

// reference the filter
svg.circle1.filter = "blur";

document.body.appendChild(svg.node);
```
    

## Property name and value transforms.

Not all XML/SVG property names are JavaScript friendly. Also many of the SVG attributes are stored in inconvenient string formats. Transforms automatically convert property names

```Javascript
node.fontFamily = "Arial";  // sets the attribute font-family;
```
    
When reading the units are stripped and the value returned as a javascript Number.

```Javascript
node.units = "px"; 
node.width = 100; // sets width to "100px";
```
    
Set groups of numbers as arrays.

```Javascript
node.viewbox = [0,0,100,100]; // set view box as space delimited string "0 0 100 100"
var view = node.viewbox;  // returns an array of numbers
```
    
References are automated.

```Javascript
node.filter = createSVG("filter");
node.filter.id = "myFilter";
// the filter can be reference
node.circle.filter = node.filter  // converts to  filter = "url(#myFilter)"
node.circle.filter = "myFilter"   // converts to  filter = "url(#myFilter)"

var filterRef = node.circle.filter; // returns reference as "myFilter"
```
    
