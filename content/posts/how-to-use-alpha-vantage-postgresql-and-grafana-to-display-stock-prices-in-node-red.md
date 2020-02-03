---
title: >-
  How to use Alpha Vantage, PostgreSQL and Grafana to display stock prices in
  Node-RED
date: 2020-02-03T19:23:27.630Z
thumb_img_path: /images/grafana_dashboard.jpg
img_path: /images/grafana_dashboard.jpg
excerpt: >-
  In  this tutorial we will show how to setup Grafana, PostgresSQL and write
  stock price time series to the database. The time series will be displayed in
  the Dashboard UI with an embedded Grafana dashboard. 
layout: post
---
<meta name="description" content="In  this tutorial we will show how to setup Grafana, PostgresSQL and write stock price time series to the database. The time series will be displayed in the Dashboard UI with an embedded Grafana dashboard. ">

<meta name="keywords" content="run python scripts, libraries, virtual environments, node-red, rodened editor, demonstration flow, integromat, zapier">

In this tutorial I will show you how to query Alpha Vantage for Microsoft stock price (MSFT). We will load the stock price time series in to a PostgresSQL database and then query the database and display it in the Node-RED Dashboard UI. The time series are displayed with a Grafana dashboard that is embeded in the Dashboard UI. 

Start with installing these nodes if they are not allready installed:

- node-red-contrib-re-postgres
- node-red-dashboard

## Why use Grafana?

Grafana is an open source solution for running data analytics, pulling up metrics that make sense of the massive amount of data and to monitor our apps with the help of cool customizable dashboards.

Grafana connects with every possible data source, databases such as Graphite, Prometheus, Influx DB, ElasticSearch, MySQL, PostgreSQL etc.

The tool helps us study, analyse & monitor data over a period of time, making it easy doing time series analytics.

The framework has gained a lot of popularity in the industry and is deployed by big guns as PayPal, eBay, Intel and many more.

Visualization options such as geo maps, heat maps, histograms, all the variety of charts and graphs which a business typically requires to study data are included.

A dashboard contains several different individual panels on the grid. Each panel has different functionalities.

## What Are the Features Offered by Grafana?

This framework takes care of all the analytics. We can easily query, visualize, set up alerts, understand the data with the help of metrics.

The dashboard is continually evolving, to make sense of complex data. From displaying graphs to heatmaps, histograms, Geo maps. The tool has a plethora of visualization options to understand data as per our business requirements.

Alerts can be set up and triggered like trip wires whenever an anticipated scenario occurs. These happenings can be notified on Slack or whatever communication platform the monitoring team uses.

Grafana has native support for approx. a dozen databases. And with many more, facilitated by respective plugins.

Either host it on-prem or any cloud platform of your choice.

It has built-in support for Graphite & expressions like add, filter, avg, min, max functions etc. to custom fetch data. 

It also has a built-in Influx DB, Prometheus, ElasticSearch, CloudWatch support.

Grafana is written as a generic monitoring solution for running monitoring and analytics on pretty much anything. 

## Install Grafana

Go to Grafana installation page here: <a href="https://grafana.com/docs/grafana/latest/installation/" target="_blank">Grafana installation</a> and follow the instructions for your operating system or use a hosted cloud version. 

On the Rodened cloud platform we can install it directly from the editor by creating install scripts in the same way as explained in this blog article about using <a href="https://www.rodened.com/posts/how-to-use-python-in-node-red-1/" target="_blank">Python in Node-RED</a>
Minimum recommended memory is 255 MB and 1 CPU.

To connect to the PostgreSQL database follow the  
<a href="https://grafana.com/docs/grafana/latest/guides/getting_started/" target="_blank">getting started</a> guide at the Grafana home page.


<a href="https://gist.github.com/dexterlabora/1bffe6808d37bd96cce283939983e758" target="_blank">here</a>

## Install PostgreSQL

When creating password do not use characters like _"+><, probably best to use A to Z and numbers. Otherwise it will not be possible to install the software. You will get error messages but these will not tell you that there is bad characters in the password.
I figured it out after hours of reinstallation and using different configurations. 
If you fix the password then it is only important to have administrators rights when running the installation and it should work without any problems.

The minimum hardware requirements for running PostgreSQL is 1GB of RAM and 512MB disk space and additional disk space for repositories.

The PostgreSQL database is a reliable SQL database that works well with time series data but if you need better performance a recommendation is to use the TimescaleDB extension for PostgreSQL.

TimescaleDB is an open-source time-series database optimized for fast ingest and complex queries. It speaks "full SQL" and is correspondingly easy to use like a traditional relational database, yet scales in ways previously reserved for NoSQL databases.

This flow was setup on a Windows 10 laptop with 8GB RAM. An article explaining the setup on the Rodened editor will follow.

# Alpha Vantage

There is serveral ways to query financial time series from Alpha Vantage. A url to download daily, weekly or intraday data in json format can be used. In this example I download daily data in csv format for 100 days.
If we add outputsize=full we can download the whole time series from the start.

More information about how to use the API can be found <a href="https://www.alphavantage.co/documentation/" target="_blank">here</a> and a free API key can be downloaded from 
<a href="https://www.alphavantage.co/support/#api-key" target="_blank">here</a>.

The query used in the http request node is:

https://www.alphavantage.co/query?function=TIME_SERIES_DAILY_ADJUSTED&symbol=MSFT&apikey=demo&datatype=csv

Replace apikey demo with your key.

## The flow













