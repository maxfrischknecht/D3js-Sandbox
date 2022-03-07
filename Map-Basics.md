# Map Basics in D3js

## Projection Algorithm

A projection algorithm allows you to go from a cartographic space (latitude and longitude) to a Cartesian space (x, y)—basically a mapping of latitude and longitude to coordinates. You can think of a projection as a way to map the three-dimensional globe to a flat plane. There are many kinds of projections, but geoMercator() is normally the default value you will use:

```js
var projection = d3.geoMercator(); 
```

## Geopath Function

Geopath is a special D3 function that will map the JSON-formatted geographic data into SVG paths. The `geoPath()` function requires GeoJSON.

```js
var path = d3.geoPath().projection(projection); 
```

## Providing the Geodata

See [https://github.com/interactivethings/swiss-maps](https://github.com/interactivethings/swiss-maps)


