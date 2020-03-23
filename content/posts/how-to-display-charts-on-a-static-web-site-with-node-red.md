---
title: How to display charts on a static web site with Node-red
date: 2020-03-23T13:12:58.852Z
thumb_img_path: /images/charts.png
vega: true
layout: post
---
![](/images/charts.png)

A static web site is fast and flexible but it is not so easy to keep them updated or add interactive charts.

![](/images/editor_charts.jpg)

Here is an example of how to use Node-red in the cloud on https://www.rodened.com to update a static Hugo site on github with interactive time series line charts or heat maps with Plotly and Vega lite.

![](/images/vega.png)

To create the Plotly charts I created python scripts and used the pythonshell node to execute the script to output the chart. The time serie is the Microsoft stock price that is submitted from Alphavantage. The latest 100 days are downloaded when the script is run. For the Vega Lite chart I took one of the examples and made the output of the chart to Github in json format.

The extra node used in this example are:

* node-red-contrib-github
* node-red-contrib-pythonshell
* node-red-node-ui-vega

On Github, the theme used for the static Hugo site needs some updates in order to show the charts.

* Create shortcodes 
* In partials add javascript for plotly and vega libraries
* Include the shortcode to show the chart on a web page

## Shortcodes

For this example, I used the Hugo theme airspace-hugo. The shortcode files are placed in a folder called shortcodes. The path to the files is airspace-hugo/layouts/shortcodes/.

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

- - -

title: "Chart examples" description: Insert and update charts with python and node-red" 
draft: false 
image : "images/portfolio/work4.jpg" bg_
image: "images/featue-bg.jpg" 
category: "UI/UX Design" 
vega : true

- - -

((< load-plotly >))

Plotly Time-series example

((< plotly json="/plotly2.json" height="400px" >))

Plotly Heat map with annotation

((< plotly json="/heatmap.json" height="400px" >))

Vega-Lite, bar and line chart

((< vega id="viz" spec="/vega.json" >))

Vega, Word Cloud

(( < vega id="viz2" spec="/wordcloud.json" > ))

The double paranthesis should be double mustaches. But cannot use it here as it will display the chart instead, like below.


Vega, bar and line chart

{{ <vega id="viz" spec="/vega.json"> }}

Vega, Word Cloud

{{ <vega id="viz2" spec="/wordcloud.json"> }}

{{< load-plotly >}}

Plotly Time series example

{{< plotly json="/plotly2.json" height="400px" >}}

Plotly Heat map with annotation

{{< plotly json="/heatmap.json" height="400px" >}}

The shortcode for plotly and vega loads the json files that are downloaded to [airspace-hugo](https://github.com/ulfsv/airspace-hugo)/static/ folder from Node-red. 

To enable the Vega chart, vega :  true is added.

The flow to set up the python virtual environment , load libraries and create python script for the plotly chart is here:

```
[{"id":"290120fa.ced44","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":820,"wires":[["4fb345b2.a0d6bc"]]},{"id":"dc76a77c.b83ef8","type":"file","z":"c549c718.ffadc8","name":"plot.py","filename":"/var/lib/data/plot.py","appendNewline":true,"createDir":false,"overwriteFile":"true","encoding":"none","x":570,"y":820,"wires":[[]]},{"id":"4fb345b2.a0d6bc","type":"template","z":"c549c718.ffadc8","name":"","field":"payload","fieldType":"msg","format":"python","syntax":"plain","template":"import plotly.graph_objects as go\n\nimport pandas as pd\n\ndf = pd.read_csv('https://www.alphavantage.co/query?function=TIME_SERIES_DAILY_ADJUSTED&symbol=MSFT&apikey=demo&datatype=csv')\n\nfig = go.Figure([go.Scatter(x=df['timestamp'], y=df['high'])])\n\nfig.update_layout(title_text='MSFT symbol',\n                  xaxis_rangeslider_visible=True)\n\nfig_json = fig.to_json()\nprint(fig_json)","output":"str","x":360,"y":820,"wires":[["dc76a77c.b83ef8"]]},{"id":"7624cf27.716e7","type":"comment","z":"c549c718.ffadc8","name":"create a python script file and save to disk","info":"","x":400,"y":760,"wires":[]},{"id":"3c7bd018.89427","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":200,"y":100,"wires":[["ec7788c6.6ece08"]]},{"id":"b321bd9e.1bef9","type":"file","z":"c549c718.ffadc8","name":"","filename":"/var/lib/data/create_env.sh","appendNewline":true,"createDir":false,"overwriteFile":"true","encoding":"none","x":610,"y":100,"wires":[[]]},{"id":"ec7788c6.6ece08","type":"template","z":"c549c718.ffadc8","name":"","field":"payload","fieldType":"msg","format":"python","syntax":"plain","template":"#!/bin/bash \n\ncd /var/lib/data\nls -l\npython3 -m venv envtest\nsource envtest/bin/activate\nls -l\nwhich python","output":"str","x":360,"y":100,"wires":[["b321bd9e.1bef9"]]},{"id":"f72b3351.0328","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":180,"y":200,"wires":[["21a95a76.ca2f86"]]},{"id":"21a95a76.ca2f86","type":"exec","z":"c549c718.ffadc8","command":"chmod u+x /var/lib/data/create_env.sh","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"make create_env.sh file executable","x":440,"y":200,"wires":[["3d6e77d8.9b7cf8"],["3d6e77d8.9b7cf8"],["3d6e77d8.9b7cf8"]]},{"id":"3d6e77d8.9b7cf8","type":"debug","z":"c549c718.ffadc8","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","x":670,"y":200,"wires":[]},{"id":"bc538741.6cf588","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":320,"wires":[["9b229834.a32d88"]]},{"id":"9b229834.a32d88","type":"exec","z":"c549c718.ffadc8","command":"bash /var/lib/data/create_env.sh","addpay":false,"append":"","useSpawn":"true","timer":"","oldrc":false,"name":"execute create_env.sh","x":400,"y":320,"wires":[["f4cee9a7.8afba8"],["f4cee9a7.8afba8"],["f4cee9a7.8afba8"]]},{"id":"f4cee9a7.8afba8","type":"debug","z":"c549c718.ffadc8","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","x":650,"y":320,"wires":[]},{"id":"7c000028.5c8cf","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":540,"wires":[["f12b70f3.da8ea"]]},{"id":"f12b70f3.da8ea","type":"exec","z":"c549c718.ffadc8","command":"chmod u+x /var/lib/data/install_packages.sh","addpay":false,"append":"","useSpawn":"true","timer":"","oldrc":false,"name":"make install_packages.sh file executable","x":460,"y":540,"wires":[["bef59c67.fe5e9"],["bef59c67.fe5e9"],["bef59c67.fe5e9"]]},{"id":"bef59c67.fe5e9","type":"debug","z":"c549c718.ffadc8","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","x":730,"y":540,"wires":[]},{"id":"3e949ae3.560ec6","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":680,"wires":[["e08cc0c1.fa803"]]},{"id":"e08cc0c1.fa803","type":"exec","z":"c549c718.ffadc8","command":"bash /var/lib/data/install_packages.sh","addpay":false,"append":"","useSpawn":"true","timer":"","oldrc":false,"name":"execute /install_packages.sh","x":420,"y":680,"wires":[["519b3f1f.b478d"],["519b3f1f.b478d"],["519b3f1f.b478d"]]},{"id":"519b3f1f.b478d","type":"debug","z":"c549c718.ffadc8","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"false","x":650,"y":680,"wires":[]},{"id":"f49f5c8b.1d583","type":"comment","z":"c549c718.ffadc8","name":"create bash file that creates and activates venv","info":"In the template node we create a bash file that will create a virtual python environment and activate the environment.\n\n#!/bin/bash \n\ncd /data\nls -l\npython3 -m venv envtest\nsource envtest/bin/activate\nls -l\nwhich python","x":400,"y":60,"wires":[]},{"id":"1aae2eda.36fbf1","type":"comment","z":"c549c718.ffadc8","name":"make the bash file executable","info":"","x":340,"y":140,"wires":[]},{"id":"e117046.36d74f8","type":"comment","z":"c549c718.ffadc8","name":"make the bash file executable","info":"","x":360,"y":480,"wires":[]},{"id":"cd75ebb3.c808f8","type":"comment","z":"c549c718.ffadc8","name":"execute the bash file","info":"","x":310,"y":260,"wires":[]},{"id":"367111ea.f5661e","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":420,"wires":[["69729dd5.b5a044"]]},{"id":"b58c449d.5bfa48","type":"file","z":"c549c718.ffadc8","name":"","filename":"/var/lib/data/install_packages.sh","appendNewline":true,"createDir":false,"overwriteFile":"true","encoding":"none","x":690,"y":420,"wires":[[]]},{"id":"69729dd5.b5a044","type":"template","z":"c549c718.ffadc8","name":"file to install packages","field":"payload","fieldType":"msg","format":"python","syntax":"plain","template":"#!/bin/bash \n\ncd /var/lib/data\nsource envtest/bin/activate\nls -l\nwhich python\npip3 install plotly nbformat pandas","output":"str","x":400,"y":420,"wires":[["b58c449d.5bfa48"]]},{"id":"2b4165f0.54641a","type":"comment","z":"c549c718.ffadc8","name":"create bash file that installs packages","info":"","x":330,"y":380,"wires":[]},{"id":"7a386470.9d424c","type":"comment","z":"c549c718.ffadc8","name":"execute the bash file to install packages","info":"","x":390,"y":620,"wires":[]},{"id":"dc36a42f.227018","type":"inject","z":"c549c718.ffadc8","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":140,"y":900,"wires":[["b31c46c3.bd0868"]]},{"id":"e9299e4.48d776","type":"file","z":"c549c718.ffadc8","name":"heatmap.py","filename":"/var/lib/data/heatmap.py","appendNewline":true,"createDir":false,"overwriteFile":"true","encoding":"none","x":570,"y":900,"wires":[[]]},{"id":"b31c46c3.bd0868","type":"template","z":"c549c718.ffadc8","name":"","field":"payload","fieldType":"msg","format":"python","syntax":"plain","template":"import plotly.figure_factory as ff\n\nz = [[.1, .3, .5, .7],\n     [1.0, .8, .6, .4],\n     [.6, .4, .2, 0.0],\n     [.9, .7, .5, .3]]\n\ncolorscale = [[0, 'red'], [1, 'green']]\nfont_colors = ['white', 'black']\nfig = ff.create_annotated_heatmap(z, colorscale=colorscale, font_colors=font_colors)\n#fig.show()\nfig_json = fig.to_json()\nprint(fig_json)","output":"str","x":340,"y":900,"wires":[["e9299e4.48d776"]]}]
```

Flow to upload to Github:

```
[{"id":"d4ff9767.93b098","type":"pythonshell in","z":"9837550c.f00838","name":"plotly","pyfile":"/var/lib/data/plot.py","virtualenv":"/var/lib/data/envtest","continuous":true,"stdInData":false,"x":130,"y":140,"wires":[["79233487.81b59c","b85b9a6c.a78308"]]},{"id":"79233487.81b59c","type":"debug","z":"9837550c.f00838","name":"","active":true,"tosidebar":true,"console":false,"tostatus":true,"complete":"payload","targetType":"msg","x":590,"y":80,"wires":[]},{"id":"b85b9a6c.a78308","type":"github-repo","z":"9837550c.f00838","github":"","name":"plotly2.json","username":"ulfsv","usernameType":"str","repository":"airspace-hugo","repositoryType":"str","action":"write","branch":"master","branchType":"str","path":"static/plotly2.json","pathType":"str","pathto":"","pathtoType":"str","contents":"payload","contentsType":"msg","outputs":"0","x":590,"y":140,"wires":[]},{"id":"49249865.a10938","type":"pythonshell in","z":"9837550c.f00838","name":"plotly","pyfile":"/var/lib/data/heatmap.py","virtualenv":"/var/lib/data/envtest","continuous":true,"stdInData":true,"x":130,"y":300,"wires":[["f23eebc.52eeb18"]]},{"id":"f23eebc.52eeb18","type":"github-repo","z":"9837550c.f00838","github":"","name":"heatmap.json","username":"ulfsv","usernameType":"str","repository":"airspace-hugo","repositoryType":"str","action":"write","branch":"master","branchType":"str","path":"static/heatmap.json","pathType":"str","pathto":"","pathtoType":"str","contents":"payload","contentsType":"msg","outputs":"0","x":400,"y":300,"wires":[]},{"id":"631d4a50.1484e4","type":"comment","z":"9837550c.f00838","name":"run script heatmap.py and upload the json file to github","info":"","x":260,"y":260,"wires":[]},{"id":"a61482d0.95ce6","type":"comment","z":"9837550c.f00838","name":"run script plot.py and upload json file to your github account","info":"","x":270,"y":80,"wires":[]}]
```

Flow to generate and upload the Vega chart as json to github:

```
[{"id":"fb616e24.34d92","type":"ui_vega","z":"43442ce0.1f9d34","group":"2b37cab1.e34ba6","name":"Line & Bar Chart","order":5,"width":"10","height":"6","vega":"","x":780,"y":80,"wires":[]},{"id":"3c52260b.bee17a","type":"inject","z":"43442ce0.1f9d34","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":140,"y":80,"wires":[["3d9e05f6.1cbe8a"]]},{"id":"3d9e05f6.1cbe8a","type":"template","z":"43442ce0.1f9d34","name":"Vega-Lite Spec","field":"payload","fieldType":"msg","format":"json","syntax":"plain","template":"{\n    \"width\": \"360\",\n    \"height\": \"250\",\n    \"data\": {\n        \"values\": []\n    },\n    \"layer\": [\n        {\n            \"mark\": \"line\",\n            \"encoding\": {\n                \"x\": {\n                    \"field\": \"year\",\n                    \"type\": \"temporal\"\n                },\n                \"y\": {\n                    \"field\": \"population\",\n                    \"type\": \"quantitative\",\n                    \"scale\": {\n                        \"zero\": false\n                    }\n                }\n            }\n        },\n        {\n            \"mark\": \"bar\",\n            \"encoding\": {\n                \"x\": {\n                    \"field\": \"year\",\n                    \"type\": \"temporal\"\n                },\n                \"y\": {\n                    \"field\": \"GDP\",\n                    \"type\": \"quantitative\"\n                },\n                \"color\": {\n                    \"value\": \"#080\"\n                },\n                \"fillOpacity\": {\n                    \"value\": 0.5\n                }\n            }\n        }\n    ],\n    \"resolve\": {\n        \"scale\": {\n            \"y\": \"independent\"\n        }\n    }\n}","output":"json","x":320,"y":80,"wires":[["c04dd183.43975"]]},{"id":"c04dd183.43975","type":"template","z":"43442ce0.1f9d34","name":"Data (Population & GDP)","field":"payload.data.values","fieldType":"msg","format":"json","syntax":"plain","template":"[\n    {\n        \"year\": \"1960\",\n        \"population\": 92500572,\n        \"GDP\": 44.30734295\n    },\n    {\n        \"year\": \"1961\",\n        \"population\": 94943000,\n        \"GDP\": 53.508617739\n    },\n    {\n        \"year\": \"1962\",\n        \"population\": 95832000,\n        \"GDP\": 60.723018684\n    },\n    {\n        \"year\": \"1963\",\n        \"population\": 96812000,\n        \"GDP\": 69.498131797\n    },\n    {\n        \"year\": \"1964\",\n        \"population\": 97826000,\n        \"GDP\": 81.749006382\n    },\n    {\n        \"year\": \"1965\",\n        \"population\": 98883000,\n        \"GDP\": 90.950278258\n    },\n    {\n        \"year\": \"1966\",\n        \"population\": 99790000,\n        \"GDP\": 105.628\n    },\n    {\n        \"year\": \"1967\",\n        \"population\": 100725000,\n        \"GDP\": 123.782\n    },\n    {\n        \"year\": \"1968\",\n        \"population\": 101061000,\n        \"GDP\": 146.601\n    },\n    {\n        \"year\": \"1969\",\n        \"population\": 103172000,\n        \"GDP\": 172.204\n    },\n    {\n        \"year\": \"1970\",\n        \"population\": 104345000,\n        \"GDP\": 212.609\n    },\n    {\n        \"year\": \"1971\",\n        \"population\": 105697000,\n        \"GDP\": 240.152\n    },\n    {\n        \"year\": \"1972\",\n        \"population\": 107188000,\n        \"GDP\": 318.031\n    },\n    {\n        \"year\": \"1973\",\n        \"population\": 108079000,\n        \"GDP\": 432.083\n    },\n    {\n        \"year\": \"1974\",\n        \"population\": 110162000,\n        \"GDP\": 479.626\n    },\n    {\n        \"year\": \"1975\",\n        \"population\": 111940000,\n        \"GDP\": 521.542\n    },\n    {\n        \"year\": \"1976\",\n        \"population\": 112771000,\n        \"GDP\": 586.162\n    },\n    {\n        \"year\": \"1977\",\n        \"population\": 113863000,\n        \"GDP\": 721.412\n    },\n    {\n        \"year\": \"1978\",\n        \"population\": 114898000,\n        \"GDP\": 1013.61\n    },\n    {\n        \"year\": \"1979\",\n        \"population\": 115870000,\n        \"GDP\": 1055.01\n    },\n    {\n        \"year\": \"1980\",\n        \"population\": 116782000,\n        \"GDP\": 1105.39\n    },\n    {\n        \"year\": \"1981\",\n        \"population\": 117648000,\n        \"GDP\": 1218.99\n    },\n    {\n        \"year\": \"1982\",\n        \"population\": 118449000,\n        \"GDP\": 1134.52\n    },\n    {\n        \"year\": \"1983\",\n        \"population\": 119259000,\n        \"GDP\": 1243.32\n    },\n    {\n        \"year\": \"1984\",\n        \"population\": 120018000,\n        \"GDP\": 1318.38\n    },\n    {\n        \"year\": \"1985\",\n        \"population\": 120754000,\n        \"GDP\": 1398.89\n    },\n    {\n        \"year\": \"1986\",\n        \"population\": 121492000,\n        \"GDP\": 2078.95\n    },\n    {\n        \"year\": \"1987\",\n        \"population\": 122091000,\n        \"GDP\": 2532.81\n    },\n    {\n        \"year\": \"1988\",\n        \"population\": 122613000,\n        \"GDP\": 3071.68\n    },\n    {\n        \"year\": \"1989\",\n        \"population\": 123116000,\n        \"GDP\": 3054.91\n    },\n    {\n        \"year\": \"1990\",\n        \"population\": 123537000,\n        \"GDP\": 3132.82\n    },\n    {\n        \"year\": \"1991\",\n        \"population\": 123921000,\n        \"GDP\": 3584.42\n    },\n    {\n        \"year\": \"1992\",\n        \"population\": 124229000,\n        \"GDP\": 3908.81\n    },\n    {\n        \"year\": \"1993\",\n        \"population\": 124536000,\n        \"GDP\": 4454.14\n    },\n    {\n        \"year\": \"1994\",\n        \"population\": 124961000,\n        \"GDP\": 4907.04\n    },\n    {\n        \"year\": \"1995\",\n        \"population\": 125439000,\n        \"GDP\": 5449.12\n    },\n    {\n        \"year\": \"1996\",\n        \"population\": 125757000,\n        \"GDP\": 4833.71\n    },\n    {\n        \"year\": \"1997\",\n        \"population\": 126057000,\n        \"GDP\": 4414.73\n    },\n    {\n        \"year\": \"1998\",\n        \"population\": 126400000,\n        \"GDP\": 4032.51\n    },\n    {\n        \"year\": \"1999\",\n        \"population\": 126631000,\n        \"GDP\": 4562.08\n    },\n    {\n        \"year\": \"2000\",\n        \"population\": 126843000,\n        \"GDP\": 4887.52\n    },\n    {\n        \"year\": \"2001\",\n        \"population\": 127149000,\n        \"GDP\": 4303.54\n    },\n    {\n        \"year\": \"2002\",\n        \"population\": 127445000,\n        \"GDP\": 4115.12\n    },\n    {\n        \"year\": \"2003\",\n        \"population\": 127718000,\n        \"GDP\": 4445.66\n    },\n    {\n        \"year\": \"2004\",\n        \"population\": 127761000,\n        \"GDP\": 4815.15\n    },\n    {\n        \"year\": \"2005\",\n        \"population\": 127773000,\n        \"GDP\": 4755.41\n    },\n    {\n        \"year\": \"2006\",\n        \"population\": 127854000,\n        \"GDP\": 4530.38\n    },\n    {\n        \"year\": \"2007\",\n        \"population\": 128001000,\n        \"GDP\": 4515.26\n    },\n    {\n        \"year\": \"2008\",\n        \"population\": 128063000,\n        \"GDP\": 5037.91\n    },\n    {\n        \"year\": \"2009\",\n        \"population\": 128047000,\n        \"GDP\": 5231.38\n    },\n    {\n        \"year\": \"2010\",\n        \"population\": 128070000,\n        \"GDP\": 5700.1\n    },\n    {\n        \"year\": \"2011\",\n        \"population\": 127833000,\n        \"GDP\": 6157.46\n    },\n    {\n        \"year\": \"2012\",\n        \"population\": 127629000,\n        \"GDP\": 6203.21\n    },\n    {\n        \"year\": \"2013\",\n        \"population\": 127445000,\n        \"GDP\": 5155.72\n    },\n    {\n        \"year\": \"2014\",\n        \"population\": 127276000,\n        \"GDP\": 4850.41\n    },\n    {\n        \"year\": \"2015\",\n        \"population\": 127141000,\n        \"GDP\": 4389.48\n    },\n    {\n        \"year\": \"2016\",\n        \"population\": 126994511,\n        \"GDP\": 4926.67\n    },\n    {\n        \"year\": \"2017\",\n        \"population\": 126785797,\n        \"GDP\": 4859.95\n    },\n    {\n        \"year\": \"2018\",\n        \"population\": 126529100,\n        \"GDP\": 4970.92\n    }\n]","output":"json","x":550,"y":80,"wires":[["fb616e24.34d92","c7276b5c.33d7c8"]]},{"id":"2dbd67a1.afdca8","type":"comment","z":"43442ce0.1f9d34","name":"↓ Display on Dashboard","info":"","x":800,"y":40,"wires":[]},{"id":"28f7278a.e6d788","type":"comment","z":"43442ce0.1f9d34","name":"↑ Add Data to Spec.","info":"","x":530,"y":120,"wires":[]},{"id":"ac186d52.f8473","type":"comment","z":"43442ce0.1f9d34","name":"↓ Visualization Spec.","info":"","x":330,"y":40,"wires":[]},{"id":"66f5abb5.946524","type":"github-repo","z":"43442ce0.1f9d34","github":"","name":"","username":"ulfsv","usernameType":"str","repository":"airspace-hugo","repositoryType":"str","action":"write","branch":"master","branchType":"str","path":"static/vega.json","pathType":"str","pathto":"","pathtoType":"str","contents":"payload","contentsType":"msg","outputs":"0","x":750,"y":280,"wires":[]},{"id":"c7276b5c.33d7c8","type":"json","z":"43442ce0.1f9d34","name":"","property":"payload","action":"","pretty":false,"x":510,"y":280,"wires":[["66f5abb5.946524"]]},{"id":"172e575c.2e90a9","type":"comment","z":"43442ce0.1f9d34","name":"convert to json and upload to github","info":"","x":260,"y":280,"wires":[]},{"id":"2b37cab1.e34ba6","type":"ui_group","z":"","name":"Group 1","tab":"c24ecd54.b7ef4","order":1,"disp":false,"width":"10","collapse":false},{"id":"c24ecd54.b7ef4","type":"ui_tab","z":"","name":"[Vega-Lite] Line & Bar Chart","icon":"dashboard","order":6,"disabled":false,"hidden":false}]
```

The Vega word cloud flow:

```
[{"id":"c23adbac.e3d2f8","type":"ui_vega","z":"10bcb0c7.41fb6f","group":"56402f70.9f9b2","name":"Vega","order":1,"width":"10","height":"6","vega":"","x":650,"y":180,"wires":[]},{"id":"60105dc0.a66c54","type":"template","z":"10bcb0c7.41fb6f","name":"Compose Vega Spec.","field":"payload","fieldType":"msg","format":"json","syntax":"mustache","template":"{\n  \"width\": 480,\n  \"height\": 300,\n\n  \"data\": [\n    {\n      \"name\": \"table\",\n      \"values\": [\n          \"{{payload}}\"\n      ],\n      \"transform\": [\n        {\n          \"type\": \"countpattern\",\n          \"field\": \"data\",\n          \"case\": \"upper\",\n          \"pattern\": \"[A-Za-z][\\\\w\\\\-\\\\.']+\\\\w\",\n          \"stopwords\": \"(and|its|can|for|using|that|the|then|you|within|such|with|this|which|your|are)\"\n        },\n        {\n          \"type\": \"formula\", \"as\": \"angle\",\n          \"expr\": \"[-45, 0, 45][~~(random() * 3)]\"\n        },\n        {\n          \"type\": \"formula\", \"as\": \"weight\",\n          \"expr\": \"if(datum.text=='NODE-RED', 600, 320)\"\n        }\n      ]\n    }\n  ],\n  \"scales\": [\n    {\n      \"name\": \"color\",\n      \"type\": \"ordinal\",\n      \"domain\": {\n        \"data\": \"table\", \n        \"field\": \"text\"\n      },\n      \"range\": [\"#d5a928\", \"#652c90\", \"#939597\"]\n    }\n  ],\n  \"marks\": [\n    {\n      \"type\": \"text\",\n      \"from\": {\n        \"data\": \"table\"\n      },\n      \"encode\": {\n        \"enter\": {\n          \"text\": {\n            \"field\": \"text\"\n          },\n          \"align\": {\n            \"value\": \"center\"\n          },\n          \"baseline\": {\n            \"value\": \"alphabetic\"\n          },\n          \"fill\": {\n            \"scale\": \"color\", \n            \"field\": \"text\"\n          }\n        }\n      },\n      \"transform\": [\n        {\n          \"type\": \"wordcloud\",\n          \"size\": [500, 300],\n          \"text\": {\n            \"field\": \"text\"\n          },\n          \"rotate\": {\n            \"field\": \"datum.angle\"\n          },\n          \"font\": \"Helvetica Neue, Arial\",\n          \"fontSize\": {\n            \"field\": \"datum.count\"\n          },\n          \"fontWeight\": {\n            \"field\": \"datum.weight\"\n          },\n          \"fontSizeRange\": [8, 32],\n          \"padding\": 2\n        }\n      ]\n    }\n  ]\n}\n","output":"json","x":460,"y":180,"wires":[["c23adbac.e3d2f8","95e92f5a.f1216"]]},{"id":"ae63bc18.e4733","type":"inject","z":"10bcb0c7.41fb6f","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":160,"y":100,"wires":[["a1cd56df.dbd2c8"]]},{"id":"a1cd56df.dbd2c8","type":"template","z":"10bcb0c7.41fb6f","name":"Input Text","field":"payload","fieldType":"msg","format":"text","syntax":"plain","template":"Node-RED\nFlow-based programming for the Internet of Things\nNode-RED is a programming tool for wiring together hardware devices, APIs and online services in new and interesting ways.\nIt provides a browser-based editor that makes it easy to wire together flows using the wide range of nodes in the palette that can be deployed to its runtime in a single-click.\nNode-RED provides a browser-based flow editor that makes it easy to wire together flows using the wide range of nodes in the palette. Flows can be then deployed to the runtime in a single-click.\nJavaScript functions can be created within the editor using a rich text editor.\nA built-in library allows you to save useful functions, templates or flows for re-use.\nBuilt on Node.js\nThe light-weight runtime is built on Node.js, taking full advantage of its event-driven, non-blocking model. This makes it ideal to run at the edge of the network on low-cost hardware such as the Raspberry Pi as well as in the cloud.\nWith over 225,000 modules in Node's package repository, it is easy to extend the range of palette nodes to add new capabilities.\nSocial Development\nThe flows created in Node-RED are stored using JSON which can be easily imported and exported for sharing with others.\nAn online flow library allows you to share your best flows with the world.\nGet Started\nNode-RED is built on Node.js, taking full advantage of its event-driven, non-blocking model. This makes it ideal to run at the edge of the network on low-cost hardware such as the Raspberry Pi as well as in the cloud.","output":"str","x":340,"y":100,"wires":[["60105dc0.a66c54"]]},{"id":"550c72b5.f0329c","type":"comment","z":"10bcb0c7.41fb6f","name":"↑ Compose Vega Spec. using Mustache Template","info":"","x":550,"y":220,"wires":[]},{"id":"e6e05824.5b2c18","type":"comment","z":"10bcb0c7.41fb6f","name":"↓ Display on Dashboard","info":"","x":700,"y":140,"wires":[]},{"id":"9526f64d.ffe328","type":"comment","z":"10bcb0c7.41fb6f","name":"↓ Input Text","info":"","x":350,"y":60,"wires":[]},{"id":"89e3831.5cc158","type":"github-repo","z":"10bcb0c7.41fb6f","github":"","name":"","username":"ulfsv","usernameType":"str","repository":"airspace-hugo","repositoryType":"str","action":"write","branch":"master","branchType":"str","path":"static/wordcloud.json","pathType":"str","pathto":"","pathtoType":"str","contents":"payload","contentsType":"msg","outputs":"0","x":710,"y":320,"wires":[]},{"id":"95e92f5a.f1216","type":"json","z":"10bcb0c7.41fb6f","name":"","property":"payload","action":"","pretty":false,"x":470,"y":320,"wires":[["89e3831.5cc158"]]},{"id":"56402f70.9f9b2","type":"ui_group","z":"","name":"Group 1","tab":"4229f80a.685f98","order":2,"disp":false,"width":"10","collapse":false},{"id":"4229f80a.685f98","type":"ui_tab","z":"","name":"[Vega] Word Cloud","icon":"dashboard","order":5,"disabled":false,"hidden":false}]
```