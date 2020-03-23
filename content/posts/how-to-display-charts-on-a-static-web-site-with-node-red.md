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

In the head.html or header.html file we add the necessary javascript libraries:

head.html can be found here: [airspace-hugo](https://github.com/ulfsv/airspace-hugo)/[layouts](https://github.com/ulfsv/airspace-hugo/tree/master/layouts)/**partials**/

head.html

The following code for plotly and vega are placed in the end of the head section:

```html
  {{- if .Params.Plotly }}
    <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
{{- end }}
  
  {{ if .Params.vega }}
    <script src="https://cdn.jsdelivr.net/npm/vega@5"></script>
    <script src="https://cdn.jsdelivr.net/npm/vega-lite@2"></script>
    <script src="https://cdn.jsdelivr.net/npm/vega-embed@3"></script>
{{ end }}
</head>
```

In the content folder ( [airspace-hugo](https://github.com/ulfsv/airspace-hugo)/[content](https://github.com/ulfsv/airspace-hugo/tree/master/content)/[english](https://github.com/ulfsv/airspace-hugo/tree/master/content/english)/**project**/) I create a markdown page chartexample.md

```markdown
---
title: "Chart examples"
description: Insert and update charts with python and node-red"
draft: false
image : "images/portfolio/work4.jpg"
bg_image: "images/featue-bg.jpg"
category: "UI/UX Design"
vega : true

---
{{< load-plotly >}}

## Plotly Time series example

{{< plotly json="/plotly2.json" height="400px" >}}

## Plotly Heat map with annotation

{{< plotly json="/heatmap.json" height="400px" >}}

## Vega Lite, bar and line chart 

{{<vega id="viz" spec="/vega.json">}}
  
```

The shortcode for plotly and vega loads the json files that are downloaded to 

[airspace-hugo](https://github.com/ulfsv/airspace-hugo)/**static**/ folder from Node-red. 

To enable the Vega chart vega :  true is added.

The flow to set up the python virtual environment , load libraries and create python script for the plotly chart is here:

```
[{"id":"290120fa.ced44","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":820,"wires":[["4fb345b2.a0d6bc"]]},{"id":"dc76a77c.b83ef8","type":"file","z":"c549c718.ffadc8","name":"plot.py","filename":"/var/lib/data/plot.py","appendNewline":true,"createDir":false,"overwriteFile":"true","encoding":"none","x":570,"y":820,"wires":[[]]},{"id":"4fb345b2.a0d6bc","type":"template","z":"c549c718.ffadc8","name":"","field":"payload","fieldType":"msg","format":"python","syntax":"plain","template":"import plotly.graph_objects as go\n\nimport pandas as pd\n\ndf = pd.read_csv('https://www.alphavantage.co/query?function=TIME_SERIES_DAILY_ADJUSTED&symbol=MSFT&apikey=demo&datatype=csv')\n\nfig = go.Figure([go.Scatter(x=df['timestamp'], y=df['high'])])\n\nfig.update_layout(title_text='MSFT symbol',\n                  xaxis_rangeslider_visible=True)\n\nfig_json = fig.to_json()\nprint(fig_json)","output":"str","x":360,"y":820,"wires":[["dc76a77c.b83ef8"]]},{"id":"7624cf27.716e7","type":"comment","z":"c549c718.ffadc8","name":"create a python script file and save to disk","info":"","x":400,"y":760,"wires":[]},{"id":"3c7bd018.89427","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":200,"y":100,"wires":[["ec7788c6.6ece08"]]},{"id":"b321bd9e.1bef9","type":"file","z":"c549c718.ffadc8","name":"","filename":"/var/lib/data/create_env.sh","appendNewline":true,"createDir":false,"overwriteFile":"true","encoding":"none","x":610,"y":100,"wires":[[]]},{"id":"ec7788c6.6ece08","type":"template","z":"c549c718.ffadc8","name":"","field":"payload","fieldType":"msg","format":"python","syntax":"plain","template":"#!/bin/bash \n\ncd /var/lib/data\nls -l\npython3 -m venv envtest\nsource envtest/bin/activate\nls -l\nwhich python","output":"str","x":360,"y":100,"wires":[["b321bd9e.1bef9"]]},{"id":"f72b3351.0328","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":180,"y":200,"wires":[["21a95a76.ca2f86"]]},{"id":"21a95a76.ca2f86","type":"exec","z":"c549c718.ffadc8","command":"chmod u+x /var/lib/data/create_env.sh","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"make create_env.sh file executable","x":440,"y":200,"wires":[["3d6e77d8.9b7cf8"],["3d6e77d8.9b7cf8"],["3d6e77d8.9b7cf8"]]},{"id":"3d6e77d8.9b7cf8","type":"debug","z":"c549c718.ffadc8","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","x":670,"y":200,"wires":[]},{"id":"bc538741.6cf588","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":320,"wires":[["9b229834.a32d88"]]},{"id":"9b229834.a32d88","type":"exec","z":"c549c718.ffadc8","command":"bash /var/lib/data/create_env.sh","addpay":false,"append":"","useSpawn":"true","timer":"","oldrc":false,"name":"execute create_env.sh","x":400,"y":320,"wires":[["f4cee9a7.8afba8"],["f4cee9a7.8afba8"],["f4cee9a7.8afba8"]]},{"id":"f4cee9a7.8afba8","type":"debug","z":"c549c718.ffadc8","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","x":650,"y":320,"wires":[]},{"id":"7c000028.5c8cf","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":540,"wires":[["f12b70f3.da8ea"]]},{"id":"f12b70f3.da8ea","type":"exec","z":"c549c718.ffadc8","command":"chmod u+x /var/lib/data/install_packages.sh","addpay":false,"append":"","useSpawn":"true","timer":"","oldrc":false,"name":"make install_packages.sh file executable","x":460,"y":540,"wires":[["bef59c67.fe5e9"],["bef59c67.fe5e9"],["bef59c67.fe5e9"]]},{"id":"bef59c67.fe5e9","type":"debug","z":"c549c718.ffadc8","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","x":730,"y":540,"wires":[]},{"id":"3e949ae3.560ec6","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":680,"wires":[["e08cc0c1.fa803"]]},{"id":"e08cc0c1.fa803","type":"exec","z":"c549c718.ffadc8","command":"bash /var/lib/data/install_packages.sh","addpay":false,"append":"","useSpawn":"true","timer":"","oldrc":false,"name":"execute /install_packages.sh","x":420,"y":680,"wires":[["519b3f1f.b478d"],["519b3f1f.b478d"],["519b3f1f.b478d"]]},{"id":"519b3f1f.b478d","type":"debug","z":"c549c718.ffadc8","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","x":650,"y":680,"wires":[]},{"id":"f49f5c8b.1d583","type":"comment","z":"c549c718.ffadc8","name":"create bash file that creates and activates venv","info":"In the template node we create a bash file that will create a virtual python environment and activate the environment.\n\n#!/bin/bash \n\ncd /data\nls -l\npython3 -m venv envtest\nsource envtest/bin/activate\nls -l\nwhich python","x":400,"y":60,"wires":[]},{"id":"1aae2eda.36fbf1","type":"comment","z":"c549c718.ffadc8","name":"make the bash file executable","info":"","x":340,"y":140,"wires":[]},{"id":"e117046.36d74f8","type":"comment","z":"c549c718.ffadc8","name":"make the bash file executable","info":"","x":360,"y":480,"wires":[]},{"id":"cd75ebb3.c808f8","type":"comment","z":"c549c718.ffadc8","name":"execute the bash file","info":"","x":310,"y":260,"wires":[]},{"id":"367111ea.f5661e","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":420,"wires":[["69729dd5.b5a044"]]},{"id":"b58c449d.5bfa48","type":"file","z":"c549c718.ffadc8","name":"","filename":"/var/lib/data/install_packages.sh","appendNewline":true,"createDir":false,"overwriteFile":"true","encoding":"none","x":690,"y":420,"wires":[[]]},{"id":"69729dd5.b5a044","type":"template","z":"c549c718.ffadc8","name":"file to install packages","field":"payload","fieldType":"msg","format":"python","syntax":"plain","template":"#!/bin/bash \n\ncd /var/lib/data\nsource envtest/bin/activate\nls -l\nwhich python\npip3 install plotly nbformat pandas","output":"str","x":400,"y":420,"wires":[["b58c449d.5bfa48"]]},{"id":"2b4165f0.54641a","type":"comment","z":"c549c718.ffadc8","name":"create bash file that installs packages","info":"","x":330,"y":380,"wires":[]},{"id":"7a386470.9d424c","type":"comment","z":"c549c718.ffadc8","name":"execute the bash file to install packages","info":"","x":390,"y":620,"wires":[]},{"id":"dc36a42f.227018","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":140,"y":900,"wires":[["b31c46c3.bd0868"]]},{"id":"e9299e4.48d776","type":"file","z":"c549c718.ffadc8","name":"heatmap.py","filename":"/var/lib/data/heatmap.py","appendNewline":true,"createDir":false,"overwriteFile":"true","encoding":"none","x":570,"y":900,"wires":[[]]},{"id":"b31c46c3.bd0868","type":"template","z":"c549c718.ffadc8","name":"","field":"payload","fieldType":"msg","format":"python","syntax":"plain","template":"import plotly.figure_factory as ff\n\nz = [[.1, .3, .5, .7],\n     [1.0, .8, .6, .4],\n     [.6, .4, .2, 0.0],\n     [.9, .7, .5, .3]]\n\ncolorscale = [[0, 'red'], [1, 'green']]\nfont_colors = ['white', 'black']\nfig = ff.create_annotated_heatmap(z, colorscale=colorscale, font_colors=font_colors)\n#fig.show()\nfig_json = fig.to_json()\nprint(fig_json)","output":"str","x":340,"y":900,"wires":[["e9299e4.48d776"]]}]
```

Flow to upload to Github:

```
[{"id":"d4ff9767.93b098","type":"pythonshell in","z":"9837550c.f00838","name":"plotly","pyfile":"/var/lib/data/plot.py","virtualenv":"/var/lib/data/envtest","continuous":true,"stdInData":false,"x":130,"y":140,"wires":[["79233487.81b59c","b85b9a6c.a78308"]]},{"id":"79233487.81b59c","type":"debug","z":"9837550c.f00838","name":"","active":true,"tosidebar":true,"console":false,"tostatus":true,"complete":"payload","targetType":"msg","x":590,"y":80,"wires":[]},{"id":"b85b9a6c.a78308","type":"github-repo","z":"9837550c.f00838","github":"","name":"plotly2.json","username":"ulfsv","usernameType":"str","repository":"airspace-hugo","repositoryType":"str","action":"write","branch":"master","branchType":"str","path":"static/plotly2.json","pathType":"str","pathto":"","pathtoType":"str","contents":"payload","contentsType":"msg","outputs":"0","x":590,"y":140,"wires":[]},{"id":"49249865.a10938","type":"pythonshell in","z":"9837550c.f00838","name":"plotly","pyfile":"/var/lib/data/heatmap.py","virtualenv":"/var/lib/data/envtest","continuous":true,"stdInData":true,"x":130,"y":300,"wires":[["f23eebc.52eeb18"]]},{"id":"f23eebc.52eeb18","type":"github-repo","z":"9837550c.f00838","github":"","name":"heatmap.json","username":"ulfsv","usernameType":"str","repository":"airspace-hugo","repositoryType":"str","action":"write","branch":"master","branchType":"str","path":"static/heatmap.json","pathType":"str","pathto":"","pathtoType":"str","contents":"payload","contentsType":"msg","outputs":"0","x":400,"y":300,"wires":[]},{"id":"631d4a50.1484e4","type":"comment","z":"9837550c.f00838","name":"run script heatmap.py and upload the json file to github","info":"","x":260,"y":260,"wires":[]},{"id":"a61482d0.95ce6","type":"comment","z":"9837550c.f00838","name":"run script plot.py and upload json file to your github account","info":"","x":270,"y":80,"wires":[]}]
```