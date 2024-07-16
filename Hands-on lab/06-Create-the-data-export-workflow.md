## Exercise 6: Create the data export workflow

**Duration**: 30 minutes

In this exercise, you create a new Logic App for your data export workflow. This Logic App will execute periodically and call your ExportLicensePlates function, then conditionally send an email if there were no records to export.

### Help references

|                 |           |
| --------------- |---------- |
| **Description** | **Links** |
| What is Azure Logic Apps? | <https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview> |
| Call Azure Functions from logic apps | <https://docs.microsoft.com/azure/logic-apps/logic-apps-azure-functions> |

### Task 1: Create the Logic App

1. In the [Azure portal](https://portal.azure.com), navigate to the **hands-on-lab-SUFFIX** resource group.

   > You can get to the resource group by selecting **Resource groups** under **Azure services** on the Azure portal home page and then select the resource group from the list. If there are many resource groups in your Azure account, you can filter the list for **hands-on-lab** to reduce the resource groups listed.

2. On your resource group blade, select the **logicapp** Logic App resource in the resource group's list of services available.

3. In the **Logic App Designer**, scroll through the page until you locate the _Start with a common trigger_ section. Select the **Recurrence** trigger.

    ![The Recurrence tile is selected in the Logic App Designer.](media/logic-app-designer-recurrence.png 'Logic App Designer')

4. Enter **15** into the **Interval** box, and make sure Frequency is set to **Minute**. This can be set to an hour or some other interval, depending on business requirements.

5. Select **+ New step**.

    ![Under Recurrence, the Interval field is set to 15, and the + New step button is selected.](media/image83.png 'Logic App Designer Recurrence section')

6. Enter `Functions` in the filter box, then select the **Azure Functions** connector.

    ![Under Choose an action, Functions is typed in the search box. Under Connectors, Azure Functions is selected.](media/image85.png 'Logic App Designer Choose an action section')

7. Select your **TollBoothFunctions** Function App.

    ![Under Azure Functions, in the search results list, Azure Functions (TollBoothFunctions) is selected.](media/logic-app-function-app-action.png 'Logic App Designer Azure Functions section')

8. Select the **ExportLicensePlates** function from the list.

    ![Under Azure Functions, under Actions (2), Azure Functions (ExportLicensePlates) is selected.](media/logic-app-select-export-function.png 'Logic App Designer Azure Functions section')

    > This function does not require any parameters that need to be sent when it gets called.

9. Select **+ New step**, then search for `condition`. Select the **Condition** Control option from the Actions search result.

    ![In the logic app designer, in the ExportLicensePlates section, the parameter field is left blank. In the Choose an action box, condition is entered as the search term, and the Condition Control item is selected from the Actions list.](media/logicapp-add-condition.png 'Logic App Designer ExportLicensePlates section')

10. For the **value** field, select the **Status code** parameter. Ensure the operator is set to **is equal to**, then enter **200** in the second value field.

    > **Note**: This evaluates the status code returned from the ExportLicensePlates function, which will return a 200 code when license plates are found and exported. Otherwise, it sends a 204 (NoContent) status code when no license plates were discovered that need to be exported. We will conditionally send an email if any response other than 200 is returned.

    ![The first Condition field displays Status code. The second, drop-down menu field displays is equal to, and the third field is set to 200.](media/logicapp-condition.png 'Condition fields')

11. We will ignore the If true condition because we don't want to perform an action if the license plates are successfully exported. Select **Add an action** within the **If false** condition block.

    ![Under the Conditions field is an If true (green checkmark) section and an if false (red x) section. In the If false section, the Add an action button is selected.](media/logicapp-condition-false-add.png 'Logic App Designer Condition fields if true/false ')

12. Enter `Send an email (V2)` in the filter box, then select the **Send an email (V2)** action for Office 365 Outlook.

    ![In the Choose an action box, send an email is entered as the search term. From the Actions list, Office 365 Outlook (end an email (V2) item is selected.](media/logicapp-send-email.png 'Office 365 Outlook Actions list')

13. Select **Sign in** and sign in to your Office 365 Outlook account.

    ![In the Office 365 Outlook - Send an email box, the Sign in button is selected.](media/image93.png 'Office 365 Outlook Sign in prompt')

14. In the Send an email form, provide the following values:

     - Enter your email address in the **To** box.
     - Provide a **Subject**, such as `Toll Booth license plate export failed`.
     - Please enter a message into the **Body** and select the **Status code** from the ExportLicensePlates function to add it to the email body.

     ![In the Send an email box, fields are set to the previously defined values.](media/logicapp-send-email-form.png 'Logic App Designer, Send an email fields')

15. Select **Save** in the toolbar to save your Logic App.

16. Select **Run Trigger** and click on **Run** to execute the Logic App. You should start receiving email alerts because the license plate data is not being exported. This is because we need to finish making changes to the ExportLicensePlates function to extract the license plate data from Azure Cosmos DB, generate the CSV file, and upload it to Blob storage.

    ![The Run button is selected on the Logic Apps Designer blade toolbar.](media/logic-app-designer-run-trigger.png 'Logic Apps Designer blade')

17. While in the Logic Apps Designer, you will see the run result of each step of your workflow. A green check mark is placed next to each step that successfully executed, showing the execution time to complete. This can be used to see how each step is working, and you can select the executed step and see the raw output.

    ![In the Logic App Designer, green check marks display next to Recurrence, ExportLicensePlates, Condition, and Send an email steps of the logic app.](media/image96.png 'Logic App Designer ')

18. The Logic App will continue to run in the background, executing every 15 minutes (or whichever interval you set) until you disable it. To disable the app, go to the **Overview** blade for the Logic App and select the **Disable** button on the taskbar.

    ![The Disable button is selected on the TollBoothLogic Logic app blade toolbar menu.](media/MicrosoftTeams-image.png 'TollBoothLogic blade')