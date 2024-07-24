# MwmChart Module
`MwmChart` is a JavaScript module for creating and managing Chart.js charts. It dynamically initializes charts from elements with the class `mwm-chart` and handles resizing on window resize events.

## Installation
To use the `MwmChart` module, you need to have [Chart.js](https://www.chartjs.org/) installed.


## Usage
To use the `MwmChart` module, you need to include the `mwm-chart.js` file in your project. You can then create a new instance of the `MwmChart` class and pass in the chart configuration options.

## HTML Structure

```html
<div id="chart-1" class="mwm-chart">
  <canvas></canvas>
  <canvas></canvas>
</div>
```

## JavaScript

```javascript
import MwmChart from './mwm-chart.js';
```

## Chart Data
The chart data is passed in as an object with the following properties:

- `type`: The type of chart to create (e.g. 'line', 'bar', 'pie', etc.).

- `data`: The data to display on the chart.

- `options`: The chart options (e.g. title, legend, etc.).

## Example

```javascript
const chartData = {
  type: 'line',
  data: {
    labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
    datasets: [{
      label: 'Sales',
      data: [65, 59, 80, 81, 56, 55, 40],
      fill: false,
      borderColor: 'rgb(75, 192, 192)',
      tension: 0.1
    }]
  },
  options: {
    scales: {
      y: {
        beginAtZero: true
      }
    }
  }
};

const chart = new MwmChart('chart-1', chartData);
```

# API Documentation
MwmChart class

## Constructor
Creates a new instance of the MwmChart class.

## Methods
Intializes the chart from the element with the specified ID and chart data.

### Parameters
- `elementId` (string): The ID of the element containing the chart.
- `chartData` (object): The chart data.



