
# PaaS: Serverless architecture Hands-on Lab

## Abstract and learning objectives

In this hands-on lab, you implement an end-to-end solution using a supplied sample based on Microsoft Azure Functions, Azure Cosmos DB, Azure Event Grid, Azure Service Bus, Azure API Management, and related services. The scenario will include implementing compute, storage, workflows, and monitoring using various components of Microsoft Azure.

At the end of the hands-on lab, you will have confidence in designing, developing, and monitoring a serverless solution that is resilient, scalable, and cost-effective.

## Overview

Contoso is rapidly expanding its toll booth management business to operate in a much larger area. As this is not their primary business, which is online payment services, they struggle with scaling up to meet the upcoming demand to extract license plate information from many new tollbooths, using photos of vehicles uploaded to cloud storage. Currently, they have a manual process where they send batches of images to a 3rd-party who manually transcodes the license plates to CSV files that they send back to Contoso to upload to their online processing system.

They want to automate this process in a way that is cost-effective and scalable. They believe serverless is the best route for them but do not have the expertise to build the solution.

## Solution architecture

Below is a diagram of the solution architecture you will build in this lab. Please study this carefully to understand the whole of the solution as you are working on the various components.

   ![](media2/updatedarch.png)

This architecture diagram illustrates a serverless solution for processing vehicle photos using various Azure services. Here's a breakdown of the workflow:


The solution begins with vehicle photos being uploaded to an Azure Data Lake Storage Gen2 container as they are captured. An Azure Event Grid subscription is created against the data lake storage container. When a new blob is created, an event is triggered that calls the photo processing Azure Function endpoint, which in turn sends the photo to the Computer Vision API service to extract the license plate data. If processing is successful and the license plate number is returned. The function submits a new Event Grid event, along with the data, to an Event Grid topic with an event type called savePlateData. However, if the processing was unsuccessful, the function submits an Event Grid event to the topic with an event type called queuePlateForManualCheckup. Two separate functions are configured to trigger when new events are added to the Event Grid topic. Each filtering on a specific event type saves the relevant data to the appropriate Azure Cosmos DB collection for the outcome, using the Cosmos DB output binding. Another Azure Function saves event data to Cosmos DB and sends a message to Service Bus. Also, Azure APIs are integrated with Azure function to  retrive processed data from cosmosDB by just using ID of the license plates.

A Logic App that runs on a 15-minute interval executes an Azure Function via its HTTP trigger, responsible for obtaining new license plate data from Cosmos DB and exporting it to a new CSV file saved to Blob storage. If no new license plate records are found to export, the Logic App sends an email notification to the Customer Service department via their Office 365 subscription. Application Insights is used to monitor all Azure Functions in real-time as data is being processed through the serverless architecture. This real-time monitoring allows you to observe dynamic scaling first-hand and configure alerts when certain events take place. Azure Key Vault is used to securely store secrets, such as connection strings and access keys. Key Vault is accessed by the Function Apps through an access policy within Key Vault, assigned to each Function App's system-assigned managed identity.

This architecture leverages serverless computing (Azure Functions) and event-driven design (Event Grid, Service Bus) to create a scalable, efficient system for processing vehicle photos, extracting license plate data, and managing the information flow through various Azure services.
