# Exercise 5: Explore your data in Azure Cosmos DB

**Estimated Duration**: 15 minutes

In this exercise, you'll use the Azure Cosmos DB Data Explorer in the portal to explore both processed and unprocessed license plate data. You'll run queries to retrieve specific data, gaining hands-on experience in navigating and managing your Cosmos DB collections for efficient data analysis.

## Lab objectives

You will be able to complete the following task:

- Task 1: Use the Azure Cosmos DB Data Explorer

### Help references

|                       |                                                           |
| --------------------- | :-------------------------------------------------------: |
| **Description**       |                         **Links**                         |
| About Azure Cosmos DB | <https://docs.microsoft.com/azure/cosmos-db/introduction> |

## Task 1: Use the Azure Cosmos DB Data Explorer

1. In the [Azure portal](https://portal.azure.com), navigate to the **hands-on-lab-SUFFIX** resource group.

   > You can get to the resource group by selecting **Resource groups** under **Azure services** on the Azure portal home page and then select the resource group from the list. If there are many resource groups in your Azure account, you can filter the list for **hands-on-lab** to reduce the resource groups listed.

1. On your resource group blade, select the **cosmosdb** Azure Cosmos DB account resource in the resource group's list of services available.

   ![The Azure Cosmos DB account resource is highlighted in the list of services in the resource group.](media/resource-group-cosmos-db-account.png "Resources")

1. On the Cosmos DB blade, select **Data Explorer** from the left-hand navigation menu.

   ![In the Data Explorer blade, Data Explorer is selected from the left menu.](media/cosmosdb-data-explorer.png 'Data Explorer')

1. Expand the **LicensePlates** database and then the **Processed** collection and select **Items**. This will list each of the JSON documents added to the collection.

1. Select one of the documents to view its contents. Your functions added the first four properties. The remaining properties are standard and are assigned by Cosmos DB.

   ![In the tree-view beneath the LicensePlates Cosmos DB, the Processed collection is expanded with the Items item selected. On the Items tab, a document is selected, and its JSON data is displayed. The first four properties of the document (fileName, licensePlateText, timeStamp, and exported) are displayed along with the standard Cosmos DB properties.](media/data-explorer-processed.png 'Data Explorer')

1. Next, expand the **NeedsManualReview** collection and select **Items**.

1. Select one of the documents to view its contents. Notice that the filename is provided, as well as a property named "exported." While out of scope for this lab, those properties can be used to provide a manual process for reviewing photos and entering license plates.

   ![In the tree-view beneath the LicensePlates Cosmos DB, the NeedsManualReview collection is expanded, and the Items item is selected. On the Items tab, a document is selected, and its JSON data is displayed. The first four properties of the document (fileName, licensePlateText, timeStamp, and resolved) are shown along with the standard Cosmos DB properties.](media/data-explorer-needsreviewtask.png 'Data Explorer')

1. Select the ellipses (...) next to the **Processed** collection and select **New SQL Query**.

   ![In the tree-view beneath the LicensePlates Cosmos DB, the Processed collection is selected. From its right-click context menu, New SQL Query is selected.](media/cosmosdb-processed-sqlquery.png 'Data Explorer')

1. Paste the SQL query below into the query window (1). This query counts the number of processed documents that have not been exported:

   ```sql
   SELECT VALUE COUNT(1) FROM c WHERE c.exported = false
   ```

1. Execute the query (2) and observe the results (3). The number of processed documents that need to be exported may vary.

   ![In the Query window, the previously defined SQL query displays. Under Results, the number 906 is highlighted.](media/execute-query-cosomosdb.png 'Query 1 tab')

## Summary

In this exercise, you have used the Data Explorer feature in the Azure Cosmos DB to explore both processed and unprocessed data from the 

## You have successfully completed the Lab!