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

![](/images/1_create_project.png)

On the New Project page, enter a name for your project, select an organization and then click Create.On the New Project page, enter a name for your project, select an organization and then click Create.

![](/images/2_new_proj.png)

# Enable the Sheets API

Make sure your project is still selected as the current project.

From the navigation menu, hover over APIs and Services and select Library. 

![](/images/3_gotolib.png)

Search for and click the Google Sheets API tile.

![](/images/4_google_sheets.png)

![](/images/5_enable_gsheet.png)

Click Enable.

# Enable the Drive API

Do the smae thing to enble the Google Drive API. Make sure you are logged into the Developers Console and your project is selected as the current project.

From the navigation menu, hover over APIs and Services and select Library.

Search for and click the Google Drive API tile.

![](/images/6_searchforgoogledrive.png)

![](/images/7_enable_googledrive.png)

Click enable.

# Download Service Account Credentials

Now we will create credentials that we download as a json file and later add to the Google sheets node to configure it. To create credentials, from the navigation menu, hover over APIs and Services and select Credentials. 

![](/images/8_create_cred.png)

On the Credentials page, click Create credentials and select the Service account key option.

Set the Service account dropdown to New service account. 

![](/images/9_create_cred2.png)

Then enter the Name and select a Role for your service account.

Click Create to generate a certificate file and download it as json.

![](/images/10_create_key.png)

# Copy the service account email address

Click on Manage service account to the right.

![](/images/11_manageacc.png)

Click on the email link.

![](/images/12_select_email.png)

And copy the email address

![](/images/13_copy_email.png)

# Create a new Google spread sheet from Google Drive.

Start with creating a blank google sheets.

![](/images/createsheetongdrive.png)

Take the ID part from the https link of the spread sheet.

![](/images/14_select_id_in_gsheet.png)



The link looks similar to this: https://docs.google.com/spreadsheets/d/1C_F7--lWjn1Q3dzkNIcU66NoalhSXL3HPvsIxEUFXo0/edit#gid=0

Here the ID is 1C_F7--lWjn1Q3dzkNIcU66NoalhSXL3HPvsIxEUFXo0 which should be copied to the ID field in the node configuration.

![](/images/15_conf_gsheet_node.png)



When you have the Google sheet open click on the share button in the upper right corner. In the people field put in the email address that you copied from Service account ID and click ready.



Now you should be able to read and write to the Google sheet.

There is an ERROR message "Missing VISEO Bot Maker key - Read the documentation.".  This will not stop the flow from working.# Install extra nodes

From the manage palette, install node-red-contrib-viseo-google-authentication and node-red-contrib-viseo-google-spreadsheet.

Now you should be able to read and write to the Google sheet.

There is an ERROR message "Missing VISEO Bot Maker key - Read the documentation.".  This will not stop the flow from working.
