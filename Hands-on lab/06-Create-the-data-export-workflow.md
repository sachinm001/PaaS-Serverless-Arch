# Exercise 6: Create the data export workflow

**Duration**: 30 minutes

Here you will be using Azure Logic Apps which is a cloud-based service that enables you to automate workflows and integrate various services and applications. It provides a visual designer for building and managing workflows, making it easy to orchestrate complex processes without writing extensive code.

In this exercise, you'll develop a new Logic App to automate your data export workflow. This Logic App will be configured to execute on a scheduled basis, calling the ExportLicensePlates function to retrieve license plate data. The workflow includes a conditional logic step: if the function finds that there are no records to export, the Logic App will automatically send an email notification. This setup ensures that your team stays informed about the status of data exports, even when no new data is available, thereby streamlining communication and operational efficiency.

### Help references

|                 |           |
| --------------- |---------- |
| **Description** | **Links** |
| What is Azure Logic Apps? | <https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview> |
| Call Azure Functions from logic apps | <https://docs.microsoft.com/azure/logic-apps/logic-apps-azure-functions> |

## Task1: Finish ExportLicensePlates function code and publish it to function app.

In this task you will completing the code by performing TODO 5,6,7 steps and publishing it to the function app from visual studio.

1. Return to the LabVM and within Visual Studio navigate to the TollBooth project using the Solution Explorer.

1. From the Visual Studio **View** menu, select **Task List**.

   ![Task List is selected from the Visual Studio View menu.](media/vs-task-list.png 'Visual Studio View menu')

1. In the Task List pane at the bottom of the Visual Studio window, double-click the `TODO 5` item, which will take you to the associated `TODO` task.

   ![TODO 5 is highlighted in the Visual Studio Task List.](media/visual-studio-task-list-todo-5.png "Task List")

1. In the DatabaseMethods.cs file that is opened, update the code on the line below the `TODO 5` comment, using the following code:
   ```
   // TODO 5: Retrieve a List of LicensePlateDataDocument objects from the collectionLink where the exported value is          false.
   licensePlates = _client.CreateDocumentQuery<LicensePlateDataDocument>(collectionLink,
        new FeedOptions() { EnableCrossPartitionQuery=true,MaxItemCount = 100 })
    .Where(l => l.exported == false)
    .ToList();
   ```

1. Next, return to the `TODO` list and double-click `TODO 6`.

   ![TODO 6 is highlighted in the Visual Studio Task List.](media/visual-studio-task-list-todo-6.png "Task List")

1. This is immediately below the `TODO 5` code you just updated. For this one, delete the line of code below the `// TODO 6` comment.

1. Make sure that you deleted the following line under `TODO 6`: `licensePlates = new List<LicensePlateDataDocument>();`.

1. Return to the TODO list and double-click TODO 7.

   ![TODO 7 is highlighted in the Visual Studio Task List.](media/visual-studio-task-list-todo-7.png "Task List")

1. In the FileMethods.cs file that is opened, update the code on the line below the TODO 7 comment, using the following code:

   ```
   // TODO 7: Asynchronously upload the blob from the memory stream.
   await blob.UploadFromStreamAsync(stream);
   ```
1. Save all your changes by clicking on **Save all** button in visual studio.

   ![](media2/updated31.png)
   

## Task 2: Configure the Logic App to export data

In this task, you will be configuring logic app to export the license plate data to the storage, if there is no data to export then it will send an email to customer service regarding that.

1. In the [Azure portal](https://portal.azure.com), navigate to the **hands-on-lab-SUFFIX** resource group.

   > You can get to the resource group by selecting **Resource groups** under **Azure services** on the Azure portal home page and then select the resource group from the list. If there are many resource groups in your Azure account, you can filter the list for **hands-on-lab** to reduce the resource groups listed.

1. On your resource group blade, select the **logicapp** Logic App resource in the resource group's list of services available.

1. In the **Logic App Designer** **(1)**, click on **Add a trigger** **(2)**. 

    ![The Recurrence tile is selected in the Logic App Designer.](media2/updated15.png 'Logic App Designer')

1. In the **Add a trigger** page, Search for `Recurrence` **(1)** and select **Recurrence** **(2)**.

    ![](media2/updated16.png)

1. Enter **15** into the **Interval** box, and make sure Frequency is set to **Minute**. This can be set to an hour or some other interval, depending on business requirements. and click on **Save**.

    ![](media2/updated17.png)

1. Now in the **Logic App Designer** click on **+** marker and select **Add an action**.

    ![Under Recurrence, the Interval field is set to 15, and the + New step button is selected.](media2/updated18.png 'Logic App Designer Recurrence section')

1. In the **Add an action** form Enter `Functions` **(1)** in the filter box, then select the **Azure Functions** **(2)**.

    ![](media2/updated19.png)

1. Select your **TollBoothFunctions** **(1)** Function App and check the **ExportLicensePlates** **(2)** function as shown. Click on **Add action** **(3)**. 

    ![](media2/updated21new.png)

1. On the **Add action** pane, keep everything as default and click on **save** and return to the **Logic App Designer**.

    ![](media2/updated27.png)

1. Select **+** marker and select **Add an action**.

    ![In the logic app designer, in the ExportLicensePlates section, the parameter field is left blank. In the Choose an action box, condition is entered as the search term, and the Condition Control item is selected from the Actions list.](media2/updated22.png 'Logic App Designer ExportLicensePlates section')

1. search for `condition` **(1)**. Select the **Condition****(2)** Control option from the Actions search result.

    ![](media2/updated23.png)

1. On the **Condition** pane, select **choose value** box and click on the symbol which represents the function app as shown.

    ![](media2/updated24.png)

1. Scroll down and choose the **Status code** option.

    ![](media2/updated25.png)

1. On the **Condition** page, make sure the condition is set to **is equal to** and set the value as **200** and save it.

    ![](media2/updated26.png)

    > **Note**: This evaluates the status code returned from the ExportLicensePlates function, which will return a 200 code when license plates are found and exported. Otherwise, it sends a 204 (NoContent) status code when no license plates were discovered that need to be exported. We will conditionally send an email if any response other than 200 is returned.


1. We will ignore the If true condition because we don't want to perform an action if the license plates are successfully exported. Select **+** marker and click on **Add an action** within the **If false** condition block.

    ![Under the Conditions field is an If true (green checkmark) section and an if false (red x) section. In the If false section, the Add an action button is selected.](media2/updated28.png)

1. Enter `Send an email (V2)` **(1)** in the filter box, then select the **Send an email (V2)** **(2)** action for Office 365 Outlook.

    ![In the Choose an action box, send an email is entered as the search term. From the Actions list, Office 365 Outlook (end an email (V2) item is selected.](media2/updated29.png)

1. Select **Sign in** and sign in to your Office 365 Outlook account.

    ![In the Office 365 Outlook - Send an email box, the Sign in button is selected.](media2/updated30.png)

1. In the Send an email form, provide the following values:

     - Enter your email address in the **To** box.
     - Provide a **Subject**, such as `Toll Booth license plate export failed`.
     - Please enter a message into the **Body** and select the **Status code** from the ExportLicensePlates function to add it to the email body.

     ![In the Send an email box, fields are set to the previously defined values.](media/logicapp-send-email-form.png 'Logic App Designer, Send an email fields')

1. Select **Save** in the toolbar to save your Logic App.

1. Select **Run Trigger** and click on **Run** to execute the Logic App. You should start receiving email alerts because the license plate data is not being exported. This is because we need to finish making changes to the ExportLicensePlates function to extract the license plate data from Azure Cosmos DB, generate the CSV file, and upload it to Blob storage.

    ![The Run button is selected on the Logic Apps Designer blade toolbar.](media/logic-app-designer-run-trigger.png 'Logic Apps Designer blade')

1. While in the Logic Apps Designer, you will see the run result of each step of your workflow. A green check mark is placed next to each step that successfully executed, showing the execution time to complete. This can be used to see how each step is working, and you can select the executed step and see the raw output.

    ![In the Logic App Designer, green check marks display next to Recurrence, ExportLicensePlates, Condition, and Send an email steps of the logic app.](media/image96.png 'Logic App Designer ')

1. The Logic App will continue to run in the background, executing every 15 minutes (or whichever interval you set) until you disable it. To disable the app, go to the **Overview** blade for the Logic App and select the **Disable** button on the taskbar.

    ![The Disable button is selected on the TollBoothLogic Logic app blade toolbar menu.](media/MicrosoftTeams-image.png 'TollBoothLogic blade')


## Summary

In this exercise, you have created a logic app automation where it runs for every 15 minutes and export data to storage. If there is no data to export, then sends an email to customer service.
