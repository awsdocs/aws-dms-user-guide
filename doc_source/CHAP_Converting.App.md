# Converting application SQL using the AWS SCT<a name="CHAP_Converting.App"></a>

When you convert your database schema from one engine to another, you also need to update the SQL code in your applications to interact with the new database engine instead of the old one\. You can view, analyze, edit, and save the converted SQL code\.

You can use the AWS Schema Conversion Tool \(AWS SCT\) to convert the SQL code in your C\+\+, C\#, Java, or other application code\. For an Oracle to PostgreSQL conversion, you can use AWS SCT to convert SQL\*Plus code to PSQL\. 

## Overview of converting application SQL<a name="CHAP_Converting.App.Overview"></a>

To convert the SQL code in your application, you take the following high\-level steps: 
+ **Create an application conversion project** – The application conversion project is a child of the database schema conversion project\. Each database schema conversion project can have one or more child application conversion projects\. For more information, see [Creating application conversion projects in the AWS SCT](#CHAP_Converting.App.Project)\. 
+ **Analyze and convert your SQL code** – AWS SCT analyzes your application, extracts the SQL code, and creates a local version of the converted SQL for you to review and edit\. The tool doesn't change the code in your application until you are ready\. For more information, see [Analyzing and converting your SQL code using the AWS SCT](#CHAP_Converting.App.Convert)\. 
+ **Create an application assessment report** – The application assessment report provides important information about the conversion of the application SQL code from your source database schema to your target database schema\. For more information, see [Creating and using the AWS SCT assessment report](#CHAP_Converting.App.AssessmentReport)\. 
+ **Edit, apply changes to, and save your converted SQL code** – The assessment report includes a list of SQL code items that can't be converted automatically\. For these items, you can edit the SQL code manually to perform the conversion\. For more information, see [Editing and saving your converted SQL code with the AWS SCT](#CHAP_Converting.App.Edit)\. 

## Creating application conversion projects in the AWS SCT<a name="CHAP_Converting.App.Project"></a>

In the AWS Schema Conversion Tool, the application conversion project is a child of the database schema conversion project\. Each database schema conversion project can have one or more child application conversion projects\. Use the following procedure to create an application conversion project\. 

**To create an application conversion project**

1. In the AWS Schema Conversion Tool, choose **New Application** from the **Applications** menu\.   
![\[Applications menu in the main application window\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/menu-applications.PNG)

   The **New application conversion project** dialog box appears\.   
![\[The New application conversion project dialog box\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/applications-new-project.png)

1. Add the following project information\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Converting.App.html)

1. Choose **OK** to create your application conversion project\. 

   The project window opens\.  
![\[The project window\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/applications-project-window.png)

1. The first time you create an application conversion project, the project window opens automatically\. To open an existing application conversion project, select the project node in the source tree, open the context \(right\-click\) menu, and then choose **Manage application**\.   
![\[Applications in source tree with context menu\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/applications-tree.png)

1. You can add additional application conversion projects by choosing **New Application** from the **Applications** menu, or by selecting the **Applications** node in the source tree, opening the context \(right\-click\) menu, and then choosing **Add application**\. 

## Analyzing and converting your SQL code using the AWS SCT<a name="CHAP_Converting.App.Convert"></a>

Use the following procedure to analyze and convert your SQL code by using the AWS Schema Conversion Tool\. 

**To analyze and convert your SQL code**

1. In the application conversion project, choose **Analyze**\. 

   AWS SCT analyzes your application code and extracts the SQL code\. A list of all extracted SQL code appears in the **Parsed SQL Scripts** pane at the bottom of the window\. The selected item in the list also appears in the **Extracted SQL script** pane\. 

1. You can analyze each SQL code item in the list, and when you are ready, choose **Convert** to convert the SQL to SQL for your target database\. 
**Note**  
You can edit the converted SQL code in a later procedure\.   
![\[SQL code to analyze\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/applications-project-analyze.png)

## Creating and using the AWS SCT assessment report<a name="CHAP_Converting.App.AssessmentReport"></a>

The *application assessment report* provides important information about converting the application SQL code from your source database schema to your target database schema\. The report details all of the SQL extracted from the application, all of the SQL converted, and action items for SQL that can't be converted\. The report also includes estimates of the amount of effort that it will take to manually convert the SQL code that can't be converted automatically\. 

### Creating an application assessment report<a name="CHAP_Converting.App.AssessmentReport.Create"></a>

Use the following procedure to create an application assessment report\.

**To create an application assessment report**

1. In the application conversion project window, choose **Create Report** from the **Actions** menu\. 

   The report is created and opens in the application conversion project window\. 

1. Review the **Summary** tab\. 

   The **Summary** tab shown following, displays the summary information from the application assessment report\. It shows the SQL code items that were converted automatically, and items that were not converted automatically\.   
![\[Application Assessment Report summary tab\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/applications-summary.png)

1. Choose the **SQL Conversion Actions** tab and review the information\. 

   The **SQL Conversion Actions** tab contains a list of SQL code items that can't be converted automatically\. There are also recommendations for how to manually convert the SQL code\. You edit your converted SQL code in a later step\. For more information, see [Editing and saving your converted SQL code with the AWS SCT](#CHAP_Converting.App.Edit)\.   
![\[SQL Conversion Actions tab\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/applications-actions.png)

1. You can save a local copy of the application assessment report as either a PDF file or a comma\-separated values \(CSV\) file\. The PDF file contains both the summary and action item information\. The CSV file contains only action item information\. 

## Editing and saving your converted SQL code with the AWS SCT<a name="CHAP_Converting.App.Edit"></a>

The assessment report includes a list of SQL code items that can't be converted automatically\. For each item that can't be converted, there is an action item on the **SQL Conversion Actions** tab\. For these items, you can edit the SQL code manually to perform the conversion\. 

Use the following procedure to edit your converted SQL code, apply the changes, and then save them\. 

**To edit, apply changes to, and save your converted SQL code**

1. Edit your converted SQL code directly in the **Target SQL script** pane\. If there is no converted code shown, you can click in the pane and start typing\. 

1. After you are finished editing your converted SQL code, choose **Apply**\. At this point, the changes are saved in memory, but not yet written to your file\. 

1. Choose **Save** to save your changes to your file\. 
**Important**  
When you choose **Save** you overwrite your original file\. Make a copy of your original file before saving so you have a record of your original application code\. 