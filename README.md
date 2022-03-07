# Notes on D3js

Source: https://learning.oreilly.com/library/view/learning-d3-js-5/9781787280175/2a58da2a-5bd6-482c-9e02-078ccb8a264b.xhtml

## Requirements

- https://www.npmjs.com/package/topojson
- https://www.npmjs.com/package/shapefile


## SVG

SVG, an XML markup language, is designed to describe two-dimensional vector graphics. The SVG markup language resides in the DOM as a node that describes exactly how to draw a shape (a curve, line, circle, or polygon). Just like HTML, SVG tags can also be styled from standard CSS. Note that, because all commands reside in the DOM, the more shapes you have, the more nodes you have and the more work for the browser. This is important to remember because, as SVG visualizations become more complex, the less fluidly they will perform.

SVG Primitives:

- circle: A standard circle with a defined radius and position attributes
- rect: A standard rectangle with height, width, and position attributes
- polygon: Any polygon, described by a list of points
- line: A line with start and end points
- path: A complex line created through a series of drawing commands

All of them are wrapped by a `<svg width="200" height="200"></svg>` container. Each shape can be direcly accessed via CSS tag commands `line { color: red; }`.

### Polygons

Polygons are drawn trough a series of `x, y` values: `<polygon points="60,5 10,120 115,120"/> `

### Shapes

```
<path d="M 120 120 L 220 220, 420 120 Z" stroke="steelblue" 
 fill="lightyellow" stroke-width="2"></path>
```

- M: Put the pen down to start drawing at x = 120 y = 120
- L: Draw a straight line that connects (120,120) to x = 220 y = 220, then draw another straight line that connects (220,220) to x = 420 y = 120
- Z: Connect the last data point (420,120) to where we started (120,120)

You can also create curves:

```html
<path d="M 120 120 L 220 220, C 200 70 480 290 320 120 Z"></path> 
```

The command `C 200 70 480 290 320 120` translates to `C x1 y1 x2 y2 x y ` and means:

- C: Indicates that we are applying a Cubic Bézier curve, just as L in the previous example indicates a straight line
- x1 and y1: Adds a control point to influence the curve's tangent
- x2 and y2: Adds a second control point after applying x1 and y1
- x and y: Indicates the final resting place of the line

## Transform 

- Translate: Move the element
- Scale: Adjust the coordinates for all attributes in the element

### Translate

For example move the circle by x 50px and y 50px:

```html
<circle cx="62" cy="62" r="50" transform="translate(50,50)"></circle> 
```

**The translate attribute is not an absolute position. It adjusts the origin of the circle relatively to cx, cy and adds 50 to those coordinates.**


### Scale

Scale adjusts the (x, y) values across all attributes in the element.

```html
<circle cx="62" cy="62" r="50" stroke-width="5" fill="red"  
 transform="scale(2,2)"></circle>
```

The scale is going to double the cx, cy, radius, and stroke-width.


### Grouping

The group tag `<g>` is used often in SVG, especially in maps. It is used to group elements and then apply a set of attributes to that set. It provides the following benefits:

- It allows you to treat a set of shapes as a single shape for the purpose of scaling and translating.
- It prevents code duplication by allowing you to set attributes at a higher level and have them inherit all the elements included.
- Groups are essential for applying transformations to large sets of SVG nodes in a performant manner. Grouping offsets the parent group rather than modifying each of the attributes in every item of the group.


## Enter Function

`enter()` binds data to our svg shapes.

The following code executes twice (iterates) because of the array `[1, 2]`. It produces 2 rectangles with different X/Y positions.

```js
svg.selectAll('rect').data([1,2]).enter()
    .append('rect')
    .attr('x', function(d){ return d*20; })
    .attr('y', function(d){ return d*50; })
    .attr("width",50)
    .attr("height",100);
```

See `Chapter03/example-2.html` as well as `Chapter03/example-3.html` for data from objects:

```js
var data = [ 
  { 
    x:10, 
    y:10, 
    width:5, 
    height:40 
  },{ 
    x:40, 
    y:10, 
    width:100, 
    height:40 
  } 
]; 
 
  var svg = d3.select("body") 
    .append("svg") 
    .attr("width", 200) 
    .attr("height", 200); 
 
  svg.selectAll('rect').data(data).enter() 
    .append('rect') 
    .attr('x', function(d){ return d.x}) 
    .attr('y', function(d){ return d.y}) 
    .attr("width", function(d){ return d.width}) 
    .attr("height", function(d){ return d.height});
```

## Update Function

The update section is not defined with an explicit update method. D3 implies an update call if no other section is provided. 

For example: If you have a function that generates data objects with random x, y values on a loop (e.g. setInterval), every time the data changes D3js updates the visualization. Take a look at `chapter3/example4.html`.

Every 1000 milliseconds, we reinvoke the rectangles function with the same data structure but different random property attributes. Because the structure is the same, the enter() section is now skipped and only update is reapplied to the existing rectangles. This is why we get the same rectangles with different dimensions every time we plot.

## Exit Function

We've seen how one determines the starting point of our visualization and the other modifies its attributes based on new data coming in. However, the examples covered had the exact number of data elements with the same properties. What would happen if our new dataset had a different amount of items? What if it has fewer or more?

```js
  // Exit
  rect.exit().attr('test', function(d) {
    console.log('no data...')
  }).remove();
```
The exit() method serves the purpose of cleansing or cleaning the no-longer-used DOM items in our visualization. This is helpful because it allows us to join our data with DOM elements, keeping them in sync. An easy way to remember this is as follows: if there are more data elements than DOM elements, the enter() section will be invoked; if there are fewer data elements than DOM elements, the exit() section will be invoked. In the previous example, we just removed the DOM element if there was no matching data.
