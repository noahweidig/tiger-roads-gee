# TIGER Roads in Google Earth Engine

This repository hosts a visualization of the TIGER/Line® roads dataset within Google Earth Engine (GEE).  
The project includes an interactive map and formatted JavaScript code for working with TIGER roads data.  

![TIGER Roads in GEE](http://raw.githubusercontent.com/noahweidig/tiger-roads-gee/refs/heads/main/images/tiger-roads-image.png)

> [!IMPORTANT]
> In order to use this data, you must have a Google account and register for an Google Earth Engine Project.
---

## Dataset

- **Source:** U.S. Census Bureau TIGER/Line® roads 2012 - 2025  
- **Type:** Vector line features  
- **Use Case:** Visualization, spatial analysis, and overlay with other datasets in GEE

[![Google Cloud](https://img.shields.io/badge/GEE%20Interactive_App-%234285F4.svg?logo=google-cloud&logoColor=white)](https://ee-weidignc.projects.earthengine.app/view/tiger-roads)


> [!WARNING]
> This data has not been validated. While the processing only consisted of converting TIGER geodatabases into CSV files and uploading to GEE, caution is advised.

---

## Sample Script
You can visualize the data here:

[![JavaScript](https://img.shields.io/badge/GEE_JavaScript_Script-F7DF1E?logo=javascript&logoColor=000)](https://code.earthengine.google.com/680ce3bd9df770c345f8f0d69a643d3a)

### Example Code
``` javascript
var years = ee.List.sequence(2012, 2025);
var road_codes = ee.Dictionary({
  'S1100': 'FF0000',
  'S1200': 'FFA500',
  'S1400': '0000FF',
  'S1500': '008000'
});

years.getInfo().forEach(function(y) {
  var roads = ee.FeatureCollection("projects/crp-proj/assets/tigerRoads/roads" + y);
  var styled = roads.map(function(f) {
    var code = f.get('MTFCC');
    var color = ee.Algorithms.If(road_codes.contains(code), road_codes.get(code), '999999');
    return f.set('style', {color: color, width: 0.6});
  }).style({styleProperty: 'style'});
  
  // Only turn 2024 on by default
  Map.addLayer(styled, {}, y.toString(), y === 2025);
});

Map.setCenter(-87.26396241599491, 30.494407354800348, 10);

// ---------- Legend ----------
var legend = ui.Panel({
  style: {
    position: 'bottom-right',
    padding: '8px'
  }
});

legend.add(ui.Label({
  value: 'Road Types',
  style: {fontWeight: 'bold', fontSize: '14px', margin: '0 0 4px 0'}
}));

// Function to add legend items
function makeLegend(color, label) {
  var entry = ui.Panel({
    layout: ui.Panel.Layout.Flow('horizontal'),
    style: {margin: '2px 0'}
  });
  
  var colorBox = ui.Label('', {
    backgroundColor: '#' + color,
    padding: '8px',
    margin: '0 4px 0 0'
  });
  
  var desc = ui.Label(label, {margin: '0'});
  entry.add(colorBox);
  entry.add(desc);
  return entry;
}

// Add road type entries
legend.add(makeLegend('FF0000', 'Primary Road'));
legend.add(makeLegend('FFA500', 'Secondary Road'));
legend.add(makeLegend('0000FF', 'Local Road'));
legend.add(makeLegend('008000', 'Vehicular Trail'));
legend.add(makeLegend('999999', 'Other Road'));

Map.add(legend);
```
