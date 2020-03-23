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

* node-red-contrib-github
* node-red-contrib-pythonshell

On Github the theme used for the static Hugo site needs some updates in order to show the charts.

* Create shortcodes 
* In partials add javascript for plotly and vega libraries
* Include the shortcode to show the chart on a web page

## Shortcodes

For this example I used the Hugo theme airspace-hugo. The shortcode files are placed in a folder called shortcodes. The path to the files are airspace-hugo/layouts/shortcodes/.

The following files will be used:

load-plotly.html

```html
{{ if not ($.Page.Scratch.Get "plotlyloaded") }}
  {{ $.Page.Scratch.Set "plotlyloaded" 1 }}
  <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
{{ end }}
```



plotly.html

```html
{{ $json := .Get "json" }}
{{ $height := .Get "height" | default "200px" }}
<div id="{{$json}}" class="plotly" style="height:{{$height}}"></div>
<script>
Plotly.d3.json({{$json}}, function(err, fig) {
    Plotly.plot('{{$json}}', fig.data, fig.layout, {responsive: true});
});
</script>
```



vega.html

```html
<div id='{{ .Get "id" }}'></div>
<script type="text/javascript">
  var spec = '{{ .Get "spec" }}';
  vegaEmbed('#{{ .Get "id" }}', spec).then(function(result) {
    // TOOD: do something?
  }).catch(console.error);
</script>
```