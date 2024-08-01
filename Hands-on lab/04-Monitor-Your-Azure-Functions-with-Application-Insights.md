# Exercise 4: Monitor your Functions with Application Insights

**Estimated Duration**: 15 minutes

Here you will be working with Application Insights which is a service that provides powerful, real-time analytics for your applications. It helps you monitor application performance, detect issues, and gain deep insights into usage patterns.

In this exercise, you'll explore the telemetry data collected by the Application Insights account that was set up during the provisioning of your Function Apps. The integration was automatically configured by associating the Application Insights account with your Function Apps, which added the necessary telemetry key to the Function App configuration. This setup enables you to gain valuable insights into the performance, usage, and potential issues within your functions, facilitating proactive maintenance and optimization.

## Lab objectives

You will be able to complete the following tasks:

- Task 1: Use the Live Metrics Stream to monitor functions in real-time
- Task 2: Observe your functions dynamically scaling when resource-constrained

### Help references

|                 |          |
| --------------- | -------- |
| **Description** | **Link** |
| Monitor Azure Functions using Application Insights | <https://docs.microsoft.com/azure/azure-functions/functions-monitoring> |
| Live Metrics Stream: Monitor & Diagnose with 1-second latency | <https://docs.microsoft.com/azure/application-insights/app-insights-live-stream> |

## Task 1: Use the Live Metrics Stream to monitor functions in real-time

In this task you will be using Live Metrics feature of App Insights to monitor the performance of your function app. You will run the Complete Application which will process and store 1000 images which you will track in real-time.

1. Open the **appinsights** Application Insights resource from within your lab resource group.

   ![The Application Insights instance is highlighted in the resource group.](media/resource-group-application-insights.png "Application Insights")

1. In Application Insights, select **Live Metrics Stream** under **Investigate** in the left-hand navigation menu.

   ![In the TollBoothMonitor blade, in the pane under Investigate, Live Metrics Stream is selected. ](media/live-metrics-link.png 'TollBoothMonitor blade')
1. Leave the Live Metrics Stream open and return to the starter app solution in Visual Studio on the LabVM.

1. Right-click the **UploadImages** project in the Solution Explorer, select **Debug**, then **Start New Instance** from the context menu.

   ![In Solution Explorer, the UploadImages project is selected. From the context menu, Debug then Start New Instance is selected.](media/vs-debug-uploadimages.png 'Solution Explorer')

   >**Note:** Ensure the files are located under `C:\ServerlessMCW\`. If the files are located under a longer root path, such as `C:\Users\workshop\Downloads\`, then you will encounter build issues in later steps: `The specified path, file name, or both are too long. The fully qualified file name must be less than 260 characters, and the directory name must be less than 248 characters.`

1. When the console window appears, enter **1** and press **ENTER**. This action uploads a handful of car photos to the images container of your Blob storage account.

   ![A Command prompt window displays, showing images being uploaded.](media/image69.png 'Command prompt window')

1. Switch back to your browser window with the Live Metrics Stream still open within Application Insights. You should start seeing new telemetry arrive, showing the number of servers online, the incoming request rate, CPU process amount, etc. You can select some of the sample telemetry in the list to the side to view output data.

   ![The Live Metrics Stream window displays information for the two online servers. Displaying line and point graphs including incoming requests, outgoing requests, and overall health. To the side is a list of Sample Telemetry information. ](media/image70.png 'Live Metrics Stream window')
   
   >**Note**: The number of servers online here may differ for you, you can ignore that.

1. Leave the Live Metrics Stream window open once again and close the console window for the image upload. Debug the UploadImages project again, then enter **2** and press **ENTER**. This will upload 1,000 new photos.

   ![The Command prompt window displays with image uploading information.](media/image71.png 'Command prompt window')

1. Switch back to the Live Metrics Stream window and observe the activity as the photos are uploaded. You can see the number of servers online, which translates to the number of Function App instances running between both Function Apps. You should also notice things such as a steady cadence for the Request Rate monitor, the Request Duration hovering below \~200ms second, and the Incoming Requests roughly matching the Outgoing Requests.

   ![In the Live Metrics Stream window, two servers are online under Incoming Requests. The Request Rate heartbeat line graph is selected, as is the Request Duration dot graph. Under Overall Health, the Process CPU heartbeat line graph is also selected, the similarities between this graph and the Request Rate graph under Incoming Requests are highlighted for comparison.](media/image72.png 'Live Metrics Stream window')

   >**Note**: The number of servers online here may differ for you, you can ignore that.

1. After this has run for a while, close the image upload console window once again, but leave the Live Metrics Stream window open.

## Task 2: Observe your functions dynamically scaling when resource-constrained

In this task, you will change the Computer Vision API to the Free tier. This will limit the number of requests to the OCR service to 10 per minute. Once changed, run the UploadImages console app to upload 1,000 images again. The resiliency policy is programmed into the FindLicensePlateText.MakeOCRRequest method of the ProcessImage function will begin exponentially backing off requests to the Computer Vision API, allowing it to recover and lift the rate limit. This intentional delay will significantly increase the function's response time, causing the Consumption plan's dynamic scaling to kick in, allocating several more servers. You will watch all of this happen in real-time using the Live Metrics Stream view.

1. Open your Computer Vision API service by opening the **hands-on-lab-SUFFIX** resource group and then selecting the resource that starts with **computervision-**.

   ![The computervision Computer vision resource is highlighted in the list of services in the resource group.](media/resource-group-computer-vision-resource.png "Resource group")

1. Select **Pricing tier**(1) under **Resource Management** in the menu. Select the **F0 Free**(2) pricing tier, then select **Apply**(3).

   > **Note**: If you already have an **F0** free pricing tier instance, you will not be able to create another one.

   ![In the Cognitive Services blade, under Resource Management, the Pricing tier item is selected. In the Choose your pricing tier blade, the F0 Free option is selected.](media/computervision-pricing-tier.png 'Choose your pricing tier blade')

1. Switch to Visual Studio, debug the **UploadImages** project again, then enter **2** and press **ENTER**. This will upload 1,000 new photos.

   ![The Command prompt window displays image uploading information.](media/image71.png 'Command Prompt window')

1. Switch back to the Live Metrics Stream window and observe the activity as the photos are uploaded. After running for a couple of minutes, you should start to notice a few things. The Request Duration will begin to increase over time. As this happens, you should notice more servers being brought online..

   ![In the Live Metrics Stream window, 8 servers are now online.](media2/updated14.png 'Live Metrics Stream window')

   >**Note**: The number of servers online here may differ for you, you can ignore that.

1. After this has run for some time, close the UploadImages console to stop uploading photos.

1. Navigate back to the **Computer Vision** resource in the Azure portal and set the pricing tier back to **S1 Standard**.

## Summary

In this exercise, you have tracked the performance and scalablity of your application by using live metrics feature of application insights.

## You have successfully completed the Lab!
