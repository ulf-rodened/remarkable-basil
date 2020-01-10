---
title: Google cloud api
layout: page
---
<!--StartFragment-->

# Setting up our Google Cloud Platform Project

The first thing to do is to set up a new Google Cloud Platform Project and enable a few APIs at <https://console.cloud.google.com>.

We need to enable a couple of APIs in our Google Cloud Platform project: Cloud Functions API and Cloud Natural Language API. From the Cloud Console, click on the Main Menu in the Top Left corner and navigate to **Menu → APIs and Services → Dashboard.**

Click on **Enable APIs and Services.** This will bring up a screen , where you can enter Cloud Functions in the search field.

Click on Google Cloud Functions API and then click on **Enable**. This will enable the API for use in your Google Cloud Platform project.

**Repeat the same process i.e. enable the Google Cloud Natural Language API.**

To invoke the Cloud Natural Language API from an external application, we need to take care of authenticating to the Cloud Natural Language API. We shall be using an API Key that will allow us to invoke the API.

To generate an API Key, we need to visit our Cloud Console for the project again. Visit the Console and go to **APIs and Services → Library.**

Go ahead and type Cloud Natural Language API in the Search box and select it. You should see the API enabled.

Click on **MANAGE** and then click on **Credentials**.

In the Credentials screen, select Service account Key,

This will generate an API Key. Download the json credential file.
