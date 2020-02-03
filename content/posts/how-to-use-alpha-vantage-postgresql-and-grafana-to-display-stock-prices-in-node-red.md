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
In this tutorial I will show you how to query Alpha Vantage for Microsoft stock price (MSFT). We will load the stock price time series in to a PostgresSQL database and then query the database and display it in the Node-RED Dashboard UI. The time series are displayed with a Grafana dashboard that is embeded in the Dashboard UI. 

Start with installing these nodes if they are not allready installed:

- node-red-contrib-re-postgres
- node-red-dashboard

## Why use Grafana?

Grafana is an open source solution for running data analytics, pulling up metrics that make sense of the massive amount of data & to monitor our apps with the help of cool customizable dashboards.

Grafana connects with every possible data source, commonly referred to as databases such as Graphite, Prometheus, Influx DB, ElasticSearch, MySQL, PostgreSQL etc.

Grafana being an open source solution also enables us to write plugins from scratch for integration with several different data sources.

The tool helps us study, analyse & monitor data over a period of time, technically called time series analytics.

Over time this framework has gained a lot of popularity in the industry & is deployed by big gunssuch as PayPal, eBay, Intel & many more.

visualization options such as geo maps, heat maps, histograms, all the variety of charts & graphs which a business typically requires to study data.

A dashboard contains several different individual panels on the grid. Each panel has different functionalities.

## What Are the Features Offered by Grafana?
Let’s go through the features offered by the open-source analytics dashboard framework.

This open-source framework takes care of all the analytics of our app. We can easily query, visualize, set up alerts, understand the data with the help of metrics.

The dashboard is pretty equipped, & continually evolving, to make sense of complex data. From displaying graphs to heatmaps, histograms, Geo maps. The tool has a plethora of visualization options to understand data as per our business requirements.

Alerts are set up & triggered like trip wires whenever an anticipated scenario occurs. These happenings can be notified on Slack or whatever communication platform the monitoring team uses.

Grafana has native support for approx. a dozen databases. And with many more, facilitated by respective plugins.

Either host it on-prem or any cloud platform of your choice.

It has built-in support for Graphite & expressions like add, filter, avg, min, max functions etc. to custom fetch data. What is Graphite? I’ll come to that.

It also has a built-in Influx DB, Prometheus, ElasticSearch, CloudWatch support.

<meta name="keywords" content="run python scripts, libraries, virtual environments, node-red, rodened editor, demonstration flow, integromat, zapier">

<a href="https://gist.github.com/dexterlabora/1bffe6808d37bd96cce283939983e758" target="_blank">here</a>

Install PostgresSQL on Windows

When creating password do not use characters like _"+>< probably best to use A to Z and numbers. Otherwise it will not be possible to install the software. 
I figured it out after hours of reinstallation and using different configurations. 
If you fix the password then it is only important to have administrators rights when 
running the installation and it should work without any problems.

https://grafana.com/docs/grafana/latest/installation/

https://grafana.com/docs/grafana/latest/guides/getting_started/

https://www.rodened.com/posts/how-to-use-python-in-node-red-1/
