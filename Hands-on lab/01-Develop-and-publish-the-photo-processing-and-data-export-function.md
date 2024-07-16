## Exercise 1: Develop and publish the photo processing and data export functions

**Duration**: 45 minutes

Use Visual Studio and its integrated Azure Functions tooling to develop and debug the functions locally and then publish them to Azure. The starter project solution, TollBooths, contains most of the code needed. You will add in the missing code before deploying to Azure.

### Help references

|                                       |                                                                        |
| ------------------------------------- | ---------------------------------------------------------------------- |
| **Description**                       | **Link**                                                              |
| Code and test Azure Functions locally | <https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-local> |

### Task 1: Open the starter solution in Visual Studio

1. On the LabVM, open File Explorer and navigate to `C:\ServerlessMCW\MCW-Serverless-architecture-main\Hands-on lab\lab-files\src\TollBooth`.

    > **Note:** Ensure the files are located under `C:\ServerlessMCW\`. If the files are located under a longer root path, such as  `C:\Users\workshop\Downloads\`, you will encounter build issues in later steps: `The specified path, file name, or both are too long. The fully qualified file name must be less than 260 characters, and the directory name must be less than 248 characters`.

2. From the **TollBooth** folder opened in step 1, open the Visual Studio Solution by double-clicking the `TollBooth.sln` file.

    ![In the TollBooth folder in File Explorer, TollBooth.sln is highlighted.](media/file-explorer-toll-booth-sln.png "File Explorer")

3. If prompted about how to open the file, select **Visual Studio 2019**, and then select **OK**.

   ![Visual Studio 2019 is highlighted in the How do you want to open this file? dialog.](media/solution-file-open-with.png "Visual Studio 2019")

4. Click on the **Sign in** button and paste your Azure account credentials given below:

   - Username: **<inject key="AzureAdUserEmail" />** and click on **Next**.

    ![](media/image2.png)

   - Password: **<inject key="AzureAdUserPassword" />** and click on **Sign in**.

    ![](media/image3.png)

5. If prompted with a security warning, uncheck **Ask me for every project in this solution**, and then select **OK**.

6. Notice the solution contains the following projects:

   - `TollBooth`
   - `UploadImages`

   > **Note**: The UploadImages project is used for uploading a handful of car photos for testing the scalability of the serverless architecture.

   ![The two projects listed above are highlighted in Solution Explorer.](media/visual-studio-solution-explorer-projects.png 'Solution Explorer')

7. To validate connectivity to your Azure subscription from Visual Studio, open **Cloud Explorer** from the **View** menu and ensure that you can connect to your Azure subscription.

   ![In Cloud Explorer, the list of Azure subscriptions is shown. A single subscription is highlighted and expanded in the list.](media/vs-cloud-explorer.png 'Cloud Explorer')

   > **Note**: You may need to select the account icon and log in with your Azure account before seeing the resources below your subscription.

8. Return to the open File Explorer window and navigate back to the **src** subfolder. From there, open the **license plates** subfolder. It contains sample license plate photos used for testing out the solution. One of the images is guaranteed to fail OCR processing, which is meant to show how the workload is designed to handle such failures. The UploadImages project uses the **copyfrom** folder as a basis for the 1,000-photo upload option for testing scalability.

### Task 2: Finish the ProcessImage function

A few components within the starter project must be completed, which are marked as `TODO` in the code. The first set of `TODO` items we address are in the `ProcessImage` function. We will update the `FindLicensePlateText` class that calls the Computer Vision service and the `SendToEventGrid` class, which is responsible for sending processing results to the Event Grid topic you created earlier.

> **Note**: **Do not** update the version of any NuGet package. This solution is built to function with the NuGet package versions currently defined within. Updating these packages to newer versions could cause unexpected results.

1. From the Visual Studio **View** menu, select **Task List**.

    ![The Visual Studio Menu displays, with View and Task List selected.](media/vs-task-list-link.png 'Visual Studio Menu')

2. There, you will see a list of `TODO` tasks, where each task represents one line of code that needs to be completed.

    ![A list of TODO tasks, including their description, project, file, and line number are displayed.](media/vs-task-list.png 'TODO tasks')

    > **Note**: If the TODO ordering is out of order, select **Description** to sort it in a logical order.

3. In the Visual Studio Solution Explorer, expand the **TollBooth** project and double-click `ProcessImage.cs` to open the file.

    > Notice the Run method is decorated with the **FunctionName** attribute, which sets the name of the Azure Function to `ProcessImage`. This is triggered by HTTP requests sent to it from the Event Grid service. You tell Event Grid that you want to get these notifications at your function's URL by creating an event subscription, which you will do in a later task, in which you subscribe to blob-created events. The function's trigger watches for new blobs being added to the images container of the data lake storage account that was created by the ARM template in the Before the hands-on lab guide. The data passed to the function from the Event Grid notification includes the URL of the blob. That URL is, in turn, passed to the input binding to obtain the uploaded image from data lake storage.

    ![The ProcessImage.cs file is highlighted within the TollBooth project in the Visual Studio Solution Explorer.](media/select-process-image.png "Solution Explorer")

4. In the **Task List** pane at the bottom of the Visual Studio window, double-click the `TODO 1` item, which will take you to the first `TODO` task.

    ![TODO 1 is highlighted in the Visual Studio Task List.](media/visual-studio-task-list-todo-1.png "Task List")

5. Update the code on the line below the `TODO 1` comment, using the following code:

    ```csharp
    // **TODO 1: Set the licensePlateText value by awaiting a new FindLicensePlateText.GetLicensePlate method.**
    licensePlateText = await new FindLicensePlateText(log, _client).GetLicensePlate(licensePlateImage);
    ```

6. Double-click `TODO 2` in the Task List to open the `FindLicensePlateText.cs` file.

    > This class is responsible for contacting the Computer Vision service's Read API to find and extract the license plate text from the photo using OCR. Notice that this class also shows how you can implement a resilience pattern using [Polly](https://github.com/App-vNext/Polly), an open-source .NET library that helps you handle transient errors. This is useful for ensuring that you do not overload downstream services, in this case, the Computer Vision service. This will be demonstrated later when visualizing the Function's scalability.

    ![TODO 2 is highlighted in the Visual Studio Task List.](media/visual-studio-task-list-todo-2.png "Task List")

7. The following code represents the completed task in FindLicensePlateText.cs:

    ```csharp
    // TODO 2: Populate the below two variables with the correct AppSettings properties.
    var uriBase = Environment.GetEnvironmentVariable("computerVisionApiUrl");
    var apiKey = Environment.GetEnvironmentVariable("computerVisionApiKey");
    ```

8. Double-click `TODO 3` in the Task List to open `SendToEventGrid.cs`.

    > This class is responsible for sending an Event to the Event Grid topic, including the event type and license plate data. Event listeners will use the event type to filter and act on the events they need to process. Please make a note of the event types defined here (the first parameter passed into the Send method), as they will be used later when creating new functions in the second Function App you provisioned earlier.

    ![TODO 3 is highlighted in the Visual Studio Task List.](media/visual-studio-task-list-todo-3.png "Task List")

9. Use the following code to complete `TODO 3` in `SendToEventGrid.cs`:

    ```csharp
    // TODO 3: Modify send method to include the proper eventType name value for saving plate data.
    await Send("savePlateData", "TollBooth/CustomerService", data);
    ```

10. `TODO 4` is  a few lines down from step 9 in the `else` block in `SendLicensePlateData(LicensePlateData data)`.  Use the following code to complete `TODO 4` in `SendToEventGrid.cs`:

    ```csharp
    // TODO 4: Modify send method to include the proper eventType name value for queuing plate for manual review.
    await Send("queuePlateForManualCheckup", "TollBooth/CustomerService", data);
    ```

    > **Note**: `TODOs` 5, 6, and 7 will be completed in later steps of the guide.

### Task 3: Publish the Function App from Visual Studio

In this task, you will publish the Function App from the starter project in Visual Studio to the existing Function App you provisioned in Azure.

1. Navigate to the **TollBooth** project using the Solution Explorer of Visual Studio.

2. Right-click the **TollBooth** project and select **Publish** from the context menu.

    ![In Solution Explorer, the TollBooth is selected, and within its context menu, the Publish item is selected.](media/image39.png "Solution Explorer")

3. In the Publish window, select **Azure**, then select **Next**.

    ![In the Pick a publish target window, the Azure Functions Consumption Plan is selected in the left pane. The Select Existing radio button is selected in the right pane, and the Run from package file (recommended) checkbox is unchecked. The Create Profile button is also selected.](media/vs-publish-function.png 'Publish window')

4. Select **Azure Function App (Windows)** for the specific target, then select **Next**.

    ![The specific target screen of the Publish dialog is shown with the Azure Function App (Windows) item selected and the Next button highlighted.](media/vs-publish-specific-target.png "Publish specific target")

5. In the **Publish** dialog:

    - Select your **Subscription** (1).
    - Select **Resource Group** under **View** (2).
    - In the **Function Apps** box (3), expand your **hands-on-lab-SUFFIX** resource group. Select the Function App whose name ends with **Functions**.
    - **Uncheck the `Run from package file` option** (4).

    ![In the App Service form, Resource Group displays in the View field, and in the tree-view below, the hands-on-lab-SUFFIX folder is expanded, and TollBoothFunctionApp is selected.](media/vs-publish-function2.png 'Publish window')

    > **Important**: We do not want to run from a package file because when we deploy from GitHub later on, the build process will be skipped if the Function App is configured for a zip deployment.

6. Select **Finish**.  This creates an Azure Function App publish XML file with a `.pubxml` extension.

7. Select **Publish** to start the process. Watch the Output window in Visual Studio as the Function App publishes. When it is finished, you should see a message that says, `========== Publish: 1 succeeded, 0 failed, 0 skipped ==========`.

    > **Note**: If prompted to update the version of the function on Azure, select **Yes**.

    ![The Publish button is selected.](media/vs-publish-function3.png "Publish")

8. Using a new tab or instance of your browser, navigate to the [Azure portal](https://portal.azure.com).

9. Open the **hands-on-lab-SUFFIX** resource group, then select the **TollBoothFunctions** Azure Function App, to which you just published.

10. Select **Functions** (1) in the left-hand navigation menu. You should see both functions you just published from the Visual Studio solution listed (2).

    ![In the Function Apps blade, in the left tree-view, both TollBoothFunctionApp and Functions (Read Only) are expanded. Beneath Functions (Read Only), two functions ExportLicensePlates and ProcessImage are highlighted.](media/dotnet-functions.png 'TollBoothFunctionApp blade')

11. Now, we need to add an Event Grid subscription to the ProcessImage function, so the function is triggered when new images are added to the data lake storage container.

    - Select the **ProcessImage** function.
    - Select **Integration** on the left-hand menu (1).
    - Select **Event Grid Trigger (eventGridEvent)** (2).
    - Select **Create Event Grid subscription** (3).

    ![In the TollboothFunctionApp tree-view, the ProcessImage function is selected. In the code window pane, the Add Event Grid subscription link is highlighted.](media/processimage-add-eg-sub.png 'ProcessImage function')

12. On the **Create Event Subscription** blade, specify the following configuration options:

    - **Name**: **Name**: Enter **processimagesub-<inject key="DeploymentID" />** (ensure the green check mark appears).
    - **Event Schema**: Select **Event Grid Schema**.
    - **Topic Type**: Select **Storage Accounts (Blob & GPv2)**.
    - **Subscription**: Select the subscription you are using for this hands-on lab.
    - **Resource Group**: Select the **hands-on-lab-SUFFIX** resource group from the list of existing resource groups.
    - **Resource**: Select your data lake storage account. This should be the only account listed and will start with `datalake`.
    - **System Topic Name**: Enter **processimagesubtopic**.
    - **Filter to Event Types**: Select only the **Blob Created** from the event types dropdown list.
    - **Endpoint Type**: Leave `Azure Function` as the Endpoint Type.
    - **Endpoint**: Leave as `ProcessImage`.

    ![In the Create event subscription form, the fields are set to the previously defined values.](media/process-image-sub-topic.png)

13. Select **Create**.