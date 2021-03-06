# Azure Data Factory Lab

## Background

A taxi commission for a major city has reached out to you for help. They are interested in understanding the predictive factors for the tips that drivers receive on different rides. 

You have a ready-made machine learning model at your disposal that will allow you to submit information about new rides and predict the tip values.

However, before you can use this model to make these prediction (or \"score\" the data), it needs to be cleaned up and transformed.

## Learning Objectives

In this lab, you will learn how to use Azure Data Factory, a no-code tool, to easily prepare your data. At the end of the lab, you will:

- be able to ingest, transform, and output data using Azure Data Factory
- create a pipeline that scores the prepared data
- understand when and how to use Azure Data Factory for other usecases

In a subsequent lab, you will also learn how to prepare the machine learning model that you are using in this lab.

But first, let\'s take a look at the data that you will be using.

## The Data

The commission has made the following data available to you.

- [Taxi ride data (one XML file)](https://adfdemostorageacctbp.blob.core.windows.net/yellow/xml/part-00000.xml)
- [Payment Lookup](https://adfdemostorageacctbp.blob.core.windows.net/yellow/lookup/payment_lookup.csv)
- [Zone Lookup](https://adfdemostorageacctbp.blob.core.windows.net/yellow/lookup/yellow_zone_lookup.csv)

## High-Level Architecture

The solution that you build in the course of the lab will take advantage of several Azure no-code services. You'll prepare your data and use it to train a machine learning model before creating a pipeline that can score any new data that you send it.

---
![architecture](images/arch.png)
---

The architecture may look complex, but the lab below will take you step-by-step through its design and development. 

## Getting Started

**Note:** In this lab, a data factory has already been created for you to use. If you are new to Azure Data Factory, you will need to create one in your own Azure subscription by following the steps [documented in this article](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal).

There are two ways of accessing the data factory.
- From the Azure Portal, select the appropriate Data Factory. Click on the Author & Monitor button pictured below:
	
	---
	![01_ADFfromPortal](images/01_ADFfromPortal.png)
	---

- Navigate directly to [https://ms-adf.azure.com/](https://ms-adf.azure.com/). You may need to select your data factory using the dropdown pictured below:

	---
	![02_SelectADF.png](images/02_SelectADF.png)
	---	

The Azure Data Factory landing page provides an overview of the tool\'s basic capabilities, as well as links to videos and in-depth tutorials. We encourage you to review some of these tutorials as you begin to use Azure Data Factory.

Click ">>" on the left-hand sidebar, and you\'ll see the three main categories of actions that you can take within Azure Data Factory:
	
---
![03_ADFmenu.png](images/03_ADFmenu.png)
***

- Author
	- In this section, you will build sequential activities for your data factory to perform.
		- Pipelines
		- Datasets
		- Dataflows
- Monitor
	- In this section, you can review your data factory\'s performance on the activities that you established for it in the Author section.
- Manage
	- In this section, you can define connection to data stores, compute, and source control for your data factory code.

## I. Creating Your First Data Factory Pipeline: Converting XML to CSV

The machine learning model will only accept CSV files for scoring. As a first step, you\'ll need to convert the XML files into CSV files.

1. If you are still on the Linked services screen, click **Author** in the left-hand sidebar. 
	1. Before building your first pipeline, toggle the **Data flow debug** setting at the top of the screen.
	2. Select "dataflowruntime1" from the **Integration runtime** dropdown.

2. You can create a pipeline in one of several ways.
	1. Click the + sign to open the **Add new resource** menu and select **Pipeline**.
	2. Click the ... ellipsis next to **Pipelines** and select **New pipeline**.

	---
	![04_ADFnewpipeline.png](images/04_ADFnewpipeline.png)
	---

3. Either way, your first pipeline will be created, and you will automatically see the pipeline authoring canvas.
	1. At left, there will be a list of pipeline **Activities** where you will be selecting the pipeline steps.
	2. In the middle, the drag and drop canvas allows you to add and link these activities.
	3. On the right, the pipeline **Properties** allows you to name and describe this pipeline.

	---
	![05_ADFpipelineproperties.png](images/05_ADFpipelineproperties.png)
	---

4. Add a descriptive name for this pipeline - such as \"nyctaxiyellow\_xml\_csv\_pl\" - and close the properties by clicking the icon above the **Properties** section.

5. To add your first pipeline activity, click on the **Move & transform** category under **Activities**.

6. Drag and drop the **Copy data** activity onto the canvas, as pictured below.

7. When you drag an activity onto the canvas, a configuration panel below the canvas will automatically expand.

	---
	![06_ADFpipelinecopydata.png](images/06_ADFpipelinecopydata.png)
	---

8. Configure your pipeline.
	1. As before, on the **General** tab, give your pipeline a descriptive name, such as \"Copy convert xml to csv\"
	2. Leave the rest of the default setting on the **General** tab.
	3. Click on the **Source** tab.
		1. Source and sink are key concepts in Azure Data Factory. They refer to the source of your data, and the destination for your data once it has been transformed.
		2. Click **New** to configure your source dataset. Connections to data sources have been configured for you; you need to do is select the appropriate *dataset* from the source.
		3. On the panel/blade that opens, select **Azure Blob Storage** and click **Continue**.

	---
	![07_newdataset.png](images/07_newdataset.png)
	---	

9.  On the next panel/blade that opens, called **Select format**, choose **XML** and click **Continue**.

	---	
	![08_newdataset_format.png](images/08_newdataset_format.png)
	---	

	1. Give this dataset a descriptive name, such as \"Yellowcab\_XML\_Data\"; then, in the **Linked service** dropdown, select the \"AzureBlobStorage1\" data source. (Once you select the data source, you may be asked to re-authenticate into Azure.)
	2. Specify "yellow" as the container and click **OK**. The data set will now appear under **Factory Resources**. 

10. Because you\'ll be working with multiple files in the same folder, you\'ll need to set up a **wildcard file path**:

	1. Expand **Wildcard file path**.
		1. Enter "xml" as the **Wildcard folder path** and "*.xml" as the **Wildcard file name**. 
	2. Disable **Recursively** and **Namespaces**.

	---
	![000_source_xml.png](images/000_source_xml.png)
	---	

11. Click on the **Sink** tab. Just as you configured the source data, you will need to configure where the CSV files are written to and stored.

	1. Click **+ New**.
	2. On the panel/blade that opens, select **Azure Blob Storage** and click **Continue**.
	3. On the **Select format** panel/blade, select **CSV/DelimitedText** and click **Continue**.
	4. As before, give this dataset a descriptive name, such as \"Yellowcab\_CSV\_Data.\" In the **Linked service** dropdown, select the \"AzureBlobStorage1\" data source you used above. (Once you select the data source, you may be asked to re-authenticate into Azure.)
		1. Select **First row as header**.
		2. Enter "yellow" as the **Container** under **File path**. 
	5.  Click **OK**.

	---
	![bp1](images/bp1.png)
	---

12. The **Sink dataset** dropdown will now read \"Yellowcab\_CSV\_Data\"

13. Click on the sink data set you just created under **Factory Resources**.

14. Click on the **Parameters** tab to configure the following three parameters.
	1. containername
	2. foldername
	3. foldername_initials_birthyear

	---
	![002_datasource_params.png](images/002_datasource_params.png)
	---

15. Once you've set up the **Parameters**, return to the **Connection** tab and click on the **Add dynamic content** link under the **Container** field. 

	---
	![container naming](images/containernaming.png)
	---

16. A new blade will open called **Add dynamic content**. Select "containername" from the list of **Parameters** at the bottom of the screen and click **OK**. 

	---
	![first parameter](images/firstparam.png)
	---

17. Repeat this process for the **Directory** field, pasting the following value into the **Add dynamic content** blade and clicking **OK**:
	- @concat(dataset().foldername,'/',dataset(foldername_initials_birthyear)

	---
	![second param](images/secondparam.png)
	---

18. Your file path shouls look like the below once you've added both parameters:

	---
	![fullpath](images/fullpath.png)
	---

19. Return to the pipeline, either by navigating through **Factory Resources** or by using the tabs above the canvas screen. You\'ll now see the following fields in your **Sink** settings.
	1. Set **containername** to \"yellow\"
	2. Set **foldername** to \"csv\"
	3. Set **foldername\_initials\_birthyear** to your initials and birth year, such as \"AB\_1970\"
	4. Set **File extension** to \".csv\"

	---
	![003_sink_params.png](images/003_sink_params.png)
	---

20. Click the **Mapping** tab.
	1. Click **Import schemas**. This will bring in the data formatting from the XML files in order to create a mapping for the CSV columns.
	2. Check the **Collection reference** box on the **record** row.
	3. As you review the columns that will be created, change each data **Type** to String. (**Note:** It may strike you as odd when some of the column will clearly be numerical values or other data types. We will be working with datatype conversions later in the lab, but if you know the data you are working with, you can certainly make these designations here.)

	---
	![004_mapping.png](images/004_mapping.png)
	---

21. You are now ready to **Validate** your first pipeline!
	1.  Click the Validate button above the canvas.
	2. Ideally, the **Pipeline validation output** will read \"Your pipeline has been validated. No errors were found.\" If you do see an error, please reach out to one of the lab coaches for assistance.

22. After validating, click the **Publish all** button to save your changes to both the pipeline and the datasets. Click **Publish** on the **Publish all** blade that appears.

	---
	![11_publish.png](images/11_publish.png)
	---

23. Now that you\'ve published your datasets and pipeline, you can test the pipeline in real-time. Click the **Debug** button above the canvas to begin pipeline run. A pipeline run status will appear below the canvas. Once you see a green check mark and the **Succeeded** status, you can hover over the pipeline run and click on the glasses icon for a detailed view.
	
	---
	![12_pipelinerun.png](images/12_pipelinerun.png)
	---

24. The **Details** popup will tell you about the pipeline run, including how much data was read, how much data was written, and the speed of the pipeline.

	---
	![13_pipelinedetails.png](images/13_pipelinedetails.png)
	---

## II. Import Dataset Schema

1. For the next pipeline, you\'ll be working with the specific data in	the CSV files that you\'ve created. To do that effectively, you will need the data schema.

2. Select the Yellowcab\_CSV\_Data dataset under **Factory Resources ->\ Datasets**, and click the **Schema** tab.
	
	---
	![14_dataset_schema.png](images/14_dataset_schema.png)
	---

3. Click **Import schema** and select **From files with \'\*.csv\'**.

	---
	![15_dataset_schema_import.png](images/15_dataset_schema_import.png)
	---

4. Verify the parameter settings in the panel/blade that opens, and click **OK**.

	---
	![16_dataset_parameters_verification.png](images/16_dataset_parameters_verification.png)
	---

5. You should now see the columns and data types as shown below. If you recall, we designated all of the columns as strings in a prior step.

	---
	![17_dataset_confirm_schema.png](images/17_dataset_confirm_schema.png)
	---

6. You can now use this schema in the data flow you\'ll create in the next section.

## III. Working with Data Flows

Though the names might differ slightly, your final dataflow will look something like this:

---
![bp2](images/bp2.png)
***

Let's walk through this process one step at a time. 

1. Create a new pipeline under **Factory Resources**. As a reminder, you can do this in one of two ways:
	1. Click the + sign to open the **Add new resource** menu and select **Pipeline**.
	2. Click the ... ellipsis next to **Pipelines** and select **New pipeline**.

2. Name your new pipeline \"nyctaxiyellow\_dataflow\_pl\"
3. As before, click on the **Move & transform** category under **Activities**.
4. This time, drag and drop the **Data flow** activity onto the canvas.
5. A panel/blade called **Adding data flow** will open automatically.

	---
	![18_dataflow.png](images/18_dataflow.png)
	---

6. Select **Create a new data flow**.

7. Select **Mapping Data Flow** and click **OK**. To learn about the two different types of data flows, visit the overviews linked below in the Resources section.

8. You will automatically be taken to the data flow canvas, with a prompt to enter your first data source.

	---
	![19_dataflow_source.png](images/19_dataflow_source.png) 
	---

9. Give your dataflow a descriptive name like \"nyctaxi\_yellow\_df\" and close the **Properties** tab.

10. Click on the **Add Source** box, and you\'ll see a quick walkthrough that explains how data flows work.

	---
	![20_dataflow_source2.png](images/20_dataflow_source2.png)
	---

11. The three data sources you\'ll be using in this data flow are:
	1. the CSV output from the prior data flow
	2. payments lookup data
	3. zone lookup data

12. Configure your first data source to look like the screenshot below:
	1. **Output stream name:** YellowTrip
	2. **Source type:** Dataset
	3. **Dataset**: Yellowcab\_CSV\_Data

	---
	![21_dataflow_source3.png](images/21_dataflow_source3.png)
	---

13.  Click on the **Projection** tab. You will see the data schema imported in the prior section. Here, you can modify the data types. Specify the following data types:

---
![22_dataflow_schema.png](images/22_dataflow_schema.png)
---

14.  You can then click the **Data preview** tab to preview your data.

15.  Now that you\'ve configured the source, you\'re ready to work with your data. Click the small + sign at the bottom right of your datasource on the canvas. You will see a list of transformation options.

---
![23_dataflow_derivedcolumns.png](images/23_dataflow_derivedcolumns.png)
---

16. Select **Derived Column**. Each transformation will have its own settings and configuration options.
	1. Give your **Output stream name** a name, such as \"DerivedColumns.\"
	2. The **Incoming stream** will auto-populate with the name of the source you specified above.

	---
	![24_dataflow_derivedcolumns2.png](images/24_dataflow_derivedcolumns2.png)
	---

17. Under **Columns**, add the following columns and expressions, clicking **+ Add column** after each one.

	Column | Expression
	------ | ---------- 
	Vendor\_abbreviation | iif(vendor\_id==1,\'CMT\',iif(vendor\_id==2,\'VTS\',\'DDS\'))
	Vendor\_description | iif(vendor\_id==1,\'Creative Mobile Technologies, LLC\',iif(vendor\_id==2,\'Verifone Inc.\',\'Digital Dispatch Systems\'))
	Pickup\_datetime | toTimestamp(pickup\_datetime,\'yyyy-MM-dd HH:mm:ss\',\'EST\')
	Dropoff\_datetime | toTimestamp(dropoff\_datetime,\'yyyy-MM-dd HH:mm:ss\',\'EST\')
	Rate\_code\_description | iif(rate\_code\_id==1, \'Standard Rate\',iif(rate\_code\_id==2,\'JFK\',iif(rate\_code\_id==3,\'Newark\',iif(rate\_code\_id==4,\'Nassau or Westchester\',iif(rate\_code\_id==5,\'Negotiated fare\',iif(rate\_code\_id==6,\'Group ride\',\'Unknown\'))))))
	Pickup\_year | year(toTimestamp(pickup\_datetime,\'yyyy-MM-dd HH:mm:ss\',\'EST\'))
	Pickup\_month | month(toTimestamp(pickup\_datetime,\'yyyy-MM-dd HH:mm:ss\',\'EST\'))
	Pickup\_day | dayOfMonth(toTimestamp(pickup\_datetime,\'yyyy-MM-dd HH:mm:ss\',\'EST\'))
	Pickup\_hour | hour(toTimestamp(pickup\_datetime,\'yyyy-MM-dd HH:mm:ss\',\'EST\'))
	Dropoff\_year | year(toTimestamp(dropoff\_datetime,\'yyyy-MM-dd HH:mm:ss\',\'EST\'))
	Dropoff\_month | month(toTimestamp(dropoff\_datetime,\'yyyy-MM-dd HH:mm:ss\',\'EST\'))
	Dropoff\_day | dayOfMonth(toTimestamp(dropoff\_datetime,\'yyyy-MM-dd HH:mm:ss\',\'EST\'))
	Dropoff\_hour | hour(toTimestamp(dropoff\_datetime,\'yyyy-MM-dd HH:mm:ss\',\'EST\'))

	Your final list of columns should look like this:

	---
	![derived columns](images/derivedcolumns.png)
	---

18. If you click on **Inspect**, you can see the new columns that will be created. **Data preview** will show you a selection of the data in those columns.

19. Click the + sign at the bottom-left of your newly-created DerivedColumns step, and select **Join**.

20. You\'ll see another set of configuration options for the Join. Change the name to \"PaymentJoin.\"

	---
	![25_dataflow_join.png](images/25_dataflow_join.png)
	---

21. In order to complete this join, you\'ll need to add another data source. The **Left stream** will default to the output from the prior step. To set up the **Right stream**, you\'ll need to click **Add Source**.
	1. A new set of **Source settings** will appear.
	2. Call this **Output stream name** \"PaymentLookup.\"
	3. This **Dataset** will not yet appear in the dropdown, so you\'ll need to click **+ New** and add it.
		1. On the **New dataset** blade, select **Azure Blob Storage** and click **Continue**.
		2. Choose **CSV/DelimitedText** as the file format, and click **Continue**.
		3. Edit the properties on the **Set properties** blade and click **OK.**
			1.  **Name**: \"Payment\_Lookup\_Data\"
			2.  **Linked service:** do not change
			3.  **File path**: yellow / lookup / payment\_lookup.csv
			---
			![26_dataflow_join_properties.png](images/26_dataflow_join_properties.png)
			---
	4. Click on the **Projection** tab and change the \"payment\_type\" to short.

	---
	![bp3](images/bp3.png)
	---

	5. Preview the data by clicking on **Data preview**.

	---
	![bp4](images/bp4.png)
	---

22. With the new data source ready, click back on the **Join** segment of the data flow. You will now see the PaymentLookup data source in the **Right stream** dropdown.

	---
	![27_dataflow_join_properties2.png](images/27_dataflow_join_properties2.png)
	---

23. Select payment\_type in both dropdowns - the **Left: DerivedColumns\'s column** and in **Right: PaymentLookup\'s column**. This determines the column on which the two source tables will be joined.

24. Your next step will be another **Join**. For this one, let\'s set up the data source first. Click **Add Source** on the data flow canvas.

	---
	![bp5](images/bp5.png)
	---

25. As before, you\'ll need to configure this data source. Name it \"ZoneLookup\" and click the **+ New** button next to **Dataset**.
	1. Select **Azure Blob Storage** and click **OK**.
	2. Select **CSV/DelimitedText** and click **Continue**.
	3. Edit the properties on the **Set properties** blade and click **OK.**
		1. **Name**: \"Zone\_Lookup\_Data\"
		2. **Linked service**: Yellowcab\_Source\_Files
		3. **File path:** yellow / lookup / yellow\_zone\_lookup.csv
		4. **Import schema:** From connection/store
		5. Check **First row as header**.

	---
	![bp6](images/bp6.png)
	---

	4. Click the **Projection** tab and change the location\_id data **Type** to short.

	---
	![bp7](images/bp7.png)
	---

26. Next, click the + below the **PaymentJoin** block in the canvas, and select **Join** again.
	
27. Configure this **Join** using the settings below:
	1. **Output stream name:** PickupZoneJoin
	2. **Left stream:** PaymentJoin
	3. **Right stream:** ZoneLookup
	4. **Join conditions:** pickup\_location\_id == location\_id

	---
	![bp8](images/bp8.png)
	---

28. The next step you\'ll add is a **Select** step.

29. Change the **Output stream** name to \"RenamePickupZoneCols\" and rename the columns as you see below:

	---
	![bp9](images/bp9.png)
	---

30. Add another **Join**, repeating the steps above but with the
	following settings changed:
	1. **Output stream name:** DropoffZoneJoin
	2. **Left stream:** RenamePickupZoneCols
	3. **Right stream:** ZoneLookup
	4. **Join conditions:** dropoff\_location\_id == location\_id

31. Add another **Select** step. Rename it \"RenameDropoffZoneCol\"
	1. Remove the following columns from your mapping:
		1. pickup\_datetime
		2. dropoff\_datetime
		3. pickup\_location\_id
		4. dropoff\_location\_id
		5. pickup\_longitude
		6. pickup\_latitude
		7. dropoff\_longitude
		8. dropoff\_latitude
		9. location\_id (*Make sure to remove both.*)
	2. Rename the last three columns to avoid a naming clash between borough, zone, and service\_zone columns:

	---
	![28_dataflow_select.png](images/28_dataflow_select.png)
	---
 
32. You\'re almost done!

33. We will need to configure a destination for the data that is transformed. Your list of **Factory Resources** has grown. Click the ... ellipses next to **Datasets** and select **New dataset**.

	---
	![29_dataset_sink.png](images/29_dataset_sink.png)
	---

	1. Configure the new dataset.
		1. Select **Azure Blob Storage** and click **Continue**.
		2. Select **CSV/DelimitedText** and click **Continue**.
		3. On the **Set properties** blade:
			1. **Name**: Dataflow\_Sink\_CSV
			2. **Linked service**: Select the one you\'ve been using

	2. On the **Connection** tab, you\'ll need to configure the **File path** using dynamic parameters similar to the ones you\'ve configured before. 
	
	---
	![second sink](images/sink2.png)
	---
	
	Your settings should ultimately look like the settings below, using the following for the **Directory** field:
	- @concat(dataset().foldername,'/',dataset().foldername_initials_birthyear)

	---
	![30_dataset_sink_settings.png](images/30_dataset_sink_settings.png)
	---

34. Return to your dataflow canvas and click the **+** sign one more time, and select **Destination -\ Sink**. A sink is the destination for your data once you\'ve completed all of these transformation steps.
	1. On the **Sink** tab, configure the following settings:
		1. Output **stream name**: DataflowSinkCSV
		**Dataset**: Dataflow\_Sink\_CSV (the dataset you created above should be available in the dropdown)
	2. On the **Settings** tab, configure the following settings:
		1. **Clear the folder:** ON
		2. **File name option**: Output to single file
		3. You may see an error here that asks you to Set single partition. If you do, click that button before proceeding.
		4. **Output to single file**: nyctaxiyellow\_final.csv

	---
	![final sink](images/sinkimage.png)
	---

35. Finally, return to the nyctaxiyellow\_dataflow\_pl pipeline.

36. Click on the **Mapping Data Flow** block on the canvas, and configure both sets of parameters in the **Settings** to reflect the containername, foldername, and initials\_birthyear information. Make sure that the **Run on (Azure IR)** field reads "dataflowruntime2".

	---
	![parameters for validate](images/paramsbeforevalidate.png)
	---

37. Click **Validate all** above the dataflow canvas to check your work. If you receive any error messages, please check in with one of your coaches.

38. Click **Publish all**, review the publication changes in the blade, and click **Publish** to save your work. If you receive any error messages, please check in with one of your coaches.

39. Click **Debug** above the pipeline canvas. The pipeline will be deployed, and you will receive a status update as with the prior pipeline.

	---
	![31_dataflow_pipeline_run.png](images/31_dataflow_pipeline_run.png)
	---

40. Hover over the pipeline run and select the eyeglass icon. You will see an overview of the dataflow, including the status at every step.

	---
	![32_dataflow_pipeline_run_details.png](images/32_dataflow_pipeline_run_details.png)
	---

## Building a Machine Learning Model

The next and final pipeline will use a machine learning model that you develop using Azure's no-code drag & drop machine learning Designer.

Before setting up this pipeline, you'll need to train the model in that service.

Please follow the instructions for the [**Machine Learning Lab**](DocumentationMLLab/mllab.md) and return here when you've completed the model.

1. Now that your model is complete, create one more pipeline. Call this pipeline \"nyctaxiyellow\_ml\_scoring\_pl" or something similar. 

2. Select the ***Machine Learning Execute** activity under **Activities** and drag it onto the canvas. 

3. On the **Parameters** tab, configure the settings below: 
	1. **Name**: Output_Path
	2. **Type**: String
	3. **Default value**: /output/output_name.csv

	---
	![bp10](images/bp10.png)
	---

4. Click on the Machine Learning Execute Pipeline block on the canvas and configure your settings to resemble the settings below. 
	1. Your **Machine Learning pipeline name** and **Machine Learning pipeline ID** will autopopulate from the pipeline you published in the Machine Learning lab. 
	2. You will need to configure the parameter in the Output_Path like you configured the other parameters above. 

5. As with the prior pipelines, verify your configuration by clicking **Validate all**, publish your work, and click **Debug** to see your Machine Learning pipeline in action! 

## Additional References

1. [Introduction to Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/introduction)

2. [Mapping data flows in Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/concepts-data-flow-overview)

3. [Wrangling data flows in Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/wrangling-data-flow-overview)