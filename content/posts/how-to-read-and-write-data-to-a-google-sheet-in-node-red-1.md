---
title: How to read and write data to a Google sheet in node-red
date: 2020-01-02T13:37:49.425Z
thumb_img_path: /images/alarm-clock-lying-on-1200.jpg
excerpt: >-
  Here we show you how easy it is to collect email from users through a submit
  form and store them in a Google sheet and read the data back in to node-red.
layout: post
---
# Install extra nodes

From the manage palette, install node-red-contrib-viseo-google-authentication and node-red-contrib-viseo-google-spreadsheet.

# Set up a Google service account

Go to <https://console.developers.google.com/project> and sign in to your Google/Gmail account.

Once signed in, you'll see a list of existing projects (if there are any) or you can click Create Project to create a new one.

On the New Project page, enter a name for your project, select an organization and then click Create.

![](/images/1_create_project.png)

![](/images/2_new_proj.png)

# Enable the Sheets API

Make sure your project is still selected as the current project.

From the navigation menu, hover over APIs and Services and select Library. Search for and click the Google Sheets API tile.

![](/images/3_gotolib.png)

![](/images/4_google_sheets.png)

![](/images/5_enable_gsheet.png)

Click Enable.

# Enable the Drive API

Make sure you are logged into the Developers Console and your project is selected as the current project.

From the navigation menu, hover over APIs and Services and select Library.

Search for and click the Google Drive API tile.

![](/images/6_searchforgoogledrive.png)

![](/images/7_enable_googledrive.png)

Click enable.

# Download Service Account Credentials

To create credentials, from the navigation menu, hover over APIs and Services and select Credentials. 

![](/images/8_create_cred.png)

On the Credentials page, click Create credentials and select the Service account key option.

Set the Service account dropdown to New service account. Then enter the Name and select a Role for your service account.

![](/images/9_create_cred2.png)

**Copy the Service account ID which is an email address starting with your service account name.**

Click Create to generate a certificate file and download it as json.

![](/images/10_create_key.png)

Click on the pen to the right of the Credentials in the node, copy and paste the information in the json file in the field for JSON.

![](/images/11_manageacc.png)

# Create a new Google spread sheet from Google Drive.

Take the ID part from the https link to the spread sheet.

The link looks similar to this: https://docs.google.com/spreadsheets/d/1C_F7--lWjn1Q3dzkNIcU66NoalhSXL3HPvsIxEUFXo0/edit#gid=0

Here the ID is 1C_F7--lWjn1Q3dzkNIcU66NoalhSXL3HPvsIxEUFXo0 which should be copied to the ID field in the node configuration.

When you have the Google sheet open click on the share button in the upper right corner. In the people field put in the email address that you copied from Service account ID and click ready.

Now you should be able to read and write to the Google sheet.

There is an ERROR message "Missing VISEO Bot Maker key - Read the documentation.".  This will not stop the flow from working.# Install extra nodes

From the manage palette, install node-red-contrib-viseo-google-authentication and node-red-contrib-viseo-google-spreadsheet.

# Set up a Google service account and enable Google sheets API

Go to <https://console.developers.google.com/project> and sign in to your Google/Gmail account.

Once signed in, you'll see a list of existing projects (if there are any) or you can click Create Project to create a new one.

On the New Project page, enter a name for your project, select an organization and then click Create.

# Enable the Drive API

Make sure you are logged into the Developers Console and your project is selected as the current project.

From the navigation menu, hover over APIs and Services and select Library.

Search for and click the Google Drive API tile.

Click enable.

# Enable the Sheets API

Make sure your project is still selected as the current project.

From the navigation menu, hover over APIs and Services and select Library. Search for and click the Google Sheets API tile.

Click Enable.

# Download Service Account Credentials

To create credentials, from the navigation menu, hover over APIs and Services and select click Credentials. On the Credentials page, click Create credentials and select the Service account key option.

Set the Service account dropdown to New service account. Then enter the Name and select a Role for your service account.

Copy the Service account ID which is an email address starting with your service account name.

Click Create to generate a certificate file and download it as json.

Click on the pen to the right of the Credentials in the node, copy and paste the information in the json file in the field for JSON.

# Create a new Google spread sheet from Google Drive.

Take the ID part from the https link to the spread sheet.

The link looks similar to this: https://docs.google.com/spreadsheets/d/1C_F7--lWjn1Q3dzkNIcU66NoalhSXL3HPvsIxEUFXo0/edit#gid=0

Here the ID is 1C_F7--lWjn1Q3dzkNIcU66NoalhSXL3HPvsIxEUFXo0 which should be copied to the ID field in the node configuration.

When you have the Google sheet open click on the share button in the upper right corner. In the people field put in the email address that you copied from Service account ID and click ready.

Now you should be able to read and write to the Google sheet.

There is an ERROR message "Missing VISEO Bot Maker key - Read the documentation.".  This will not stop the flow from working.
