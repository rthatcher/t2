---
layout: default
title: Node-Red Tutorial
category: tutorials
section: tutorials
index-order: 306
sidebar: sidebars/accordion-toc0.md
excerpt: "TBC"
---

# Node-RED Tutorial for Integrating a {{site.data.conrefs.composer_full}} Business Network

In this tutorial, we will build on the [Developer Tutorial](./developer-tutorial.html), extending it to demonstrate how a Node-RED Flow can be used to add Participants or Assets to the registries on the {{site.data.conrefs.hlf_full}}.  

[Node-RED](http://nodered.org) is a lightweight Open Source integration technology, written in JavaScript. It uses a graphical flow to integrate different _nodes_, where nodes can receive data, transform data and output data.

Node-RED is commonly used to rapidly prototype Internet of Things style applications, or to wire existing Internet services together.

You can use the {{site.data.conrefs.composer_full}} Node-RED contribution to:

- Submit transactions
- Read and update assets and participants
- Subscribe to events
- Delete assets and participants

This tutorial gives an overview of the techniques and resources available to apply to your own use case.

This tutorial uses the `tutorial-network` business network developed and deployed in the [Developer Tutorial](developer-tutorial.html).

## Prerequisites

Before beginning this tutorial:

 - Complete the [development environment installation](../installing/development-tools.html).
 - Complete the [developer tutorial](./developer-tutorial.html).

This tutorial assumes the reader has some familiarity with Node-RED.  The [Node-RED](http://nodered.org) website has an introductory video and a getting started link.

## Step One: Install and Start Node-RED

Node-RED is a Node.js application and should be installed on a {{site.data.conrefs.composer}} development environment with the following command `npm install -g node-red` 

Use the following command to start Node-RED `node-red`

The command output will show that Node-Red server is now running at http://127.0.0.1:1880/ - open this link in a Browser to see the Node-RED UI.

## Step Two: Install Composer Nodes

The Composer Nodes for Node-RED are described here https://www.npmjs.com/package/node-red-contrib-composer and can be installed into Node-RED through the UI.

1. On the Node-RED UI Menu, click **Manage palette**, the click on the **Install** tab.  In the **search modules** field type _node-red-contrib-composer_, then click the **Install** button and on the confirmation dialog click the red **Install** button.

The nodes take a few moments to install, and a message will be shown in the UI and in the terminal window where Node-RED was started.

2. Verify that the nodes are installed by scrolling to the end of the Palette on the left of the UI - a new category called **Hyperledger** should be at the bottom with the three nodes.

## Step Three: Install fs Node

This tutorial uses JSON data from text files and needs additional nodes _node-red-contrib-fs_

1. On the Node-RED UI Menu, click **Manage palette**, the click on the **Install** tab.  In the **search modules** field type _node-red-contrib-fs_, then click the **Install** button and on the confirmation dialog click the red **Install** button.

2. Verify that the **fs file lister** node has been added to the Storage category on the Palette.

## Step Four: Build Flow

The image below shows what the completed flow will look like.
<!-- ![Queries exposed as REST Endpoints](../assets/img/tutorials/nodered/nodered-flow.png) -->

**Drag** the nodes from the palette on the left onto the Flow.

**Double click** the nodes to edit the properties.

The types and properties of the six nodes are described below:

1. **inject** node (from input category) - _Start_

Set the properties for this node:
 -  Payload - timestamp
 -  Topic - <empty\>
 -  Repeat - none
 -  Inject once at start? - <unchecked\>
 -  Name - Start

2. **fs file lister** node (from storage category) - _Read JSON Files_
 2. **fs file lister** node (from storage category) - _Read JSON Files_
#### 2. **fs file lister** node (from storage category) - _Read JSON Files_
##### 2. **fs file lister** node (from storage category) - _Read JSON Files_
###### 2. **fs file lister** node (from storage category) - _Read JSON Files_
####### 2. **fs file lister** node (from storage category) - _Read JSON Files_

Set the properties for this node:
 -  Start Folder - /home/<username\>/tutorial-network/testdata/traders/ \*
 -  File Pattern - \*.json
 -  Include full path in output? - <checked\>
 -  Output single message (array)? - <unchecked\>
 -  Max, search depth - 0
 -  Return file details? - <unchecked\>
 -  Name - Read JSON Files
\* On Ubuntu Linux substitute <username\> for the logged in user.  On Mac adjust the Foldername to reflect your system. 

3. **change** node (from function category) - _Set Filename_

Set the properties for this node:
 -  Name - Set Filename
 -  Rules
    -  Set - msg.filename
    -  to - msg.payload

4. **file in** node (from storage category) - _Read JSON File_

Set the properties for this node:
 -  **Filename** - <empty\>
 -  **Repeat** - a single utf8 string
 -  **Send message on error (legacy mode)** - <checked\>
 -  **Name** - Read JSON File

5. **json** node (from function category) - _Set JSON_

Set the properties for this node:
 -  **Name** - _Set JSON_
 -  **Format JSON string** - _<checked\>_

6. **composer** out node (from Hyperledger category) - _Create Trader_

Set the properties for this node:
 -  Name - _Create Trader_
 -  Action Type - _Create_

**Click** the pencil icon to add a new Composer Card.
 -  Card Name - _admin\@tutorial-network_

Remember to click the **Deploy** button to save the flow.

## Step Five: Create Test Data
#### Create JSON text files for new Participants
Using a text editor such as vi or gedit , create a text file called **/home/_username_/tutorial-network/testdata/traders/trader4.json** with the following contents
```
{
  "$class": "org.acme.biznet.Trader",
  "tradeId": "TRADER4",
  "firstName": "John",
  "lastName": "Smith"
}
```

Create two more JSON text files for **trader5** and **trader6** with the following data
```
{
  "$class": "org.acme.biznet.Trader",
  "tradeId": "TRADER5",
  "firstName": "Simon",
  "lastName": "Jenkins"
}
```
```
{
  "$class": "org.acme.biznet.Trader",
  "tradeId": "TRADER6",
  "firstName": "Susan",
  "lastName": "Stone"
}
```

#### Create JSON text files for new Commodities
Create a text file called **/home/_username_/tutorial-network/testdata/commodities/diamond.json** with the following contents
```
{
  "$class": "org.acme.biznet.Commodity",
  "tradingSymbol": "DIA",
  "description": "Diamond commodity",
  "mainExchange": "Jewel",
  "quantity": 1.45,
  "owner": "resource:org.acme.biznet.Trader#TRADER6"
}
```

Create two more JSON text files for **apple** and **oil** with the following data
```
{
  "$class": "org.acme.biznet.Commodity",
  "tradingSymbol": "APP",
  "description": "Apple commodity",
  "mainExchange": "Food",
  "quantity": 234500,
  "owner": "resource:org.acme.biznet.Trader#TRADER4"
}
```

```
{
 "$class": "org.acme.biznet.Commodity",
 "tradingSymbol": "CL",
 "description": "Crude Oil",
 "mainExchange": "NYMEX",
 "quantity": 35,
 "owner": "resource:org.acme.biznet.Trader#TRADER3"
}
```


## Step Six: Test Flow

A 



<!--  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
#### Sub-sub example

Composer name example {{site.data.conrefs.composer}} 

BOLD example **business network definition (BND)**. It 
Itailc _here_ 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Example Image
![Queries exposed as REST Endpoints](../assets/img/tutorials/nodered/rest-explorer-discover.png)
-->
