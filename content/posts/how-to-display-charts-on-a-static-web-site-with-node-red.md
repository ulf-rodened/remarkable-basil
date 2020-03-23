---
title: How to display charts on a static web site with Node-red
date: 2020-03-23T13:12:58.852Z
thumb_img_path: /images/charts.png
layout: post
---
## Update a static Hugo site with Plotly and Vega charts

Static web site are fast and flexible but is not so easy to keep them updated or add interactive charts.

Here is an example of how to use Node-red in the cloud on www.rodened.com to update a static Hugo site on github with interactive time series line charts or heat maps with Plotly and Vega lite.

To create the Plotly charts I created python scripts and used the pythonshell node to execute the script to output the chart. For the Vega Lite chart I took one of the examples and made an output of the chart to Github in json format.

Extra node used in this example are:

- node-red-contrib-github
- node-red-contrib-pythonshell

On Github the theme used for the static Hugo site needs some updates in order to show the charts.

- Create shortcodes 
- 

