---
title: "Create an OS Deployment Task Sequence Package | Microsoft Docs"
ms.custom: ""
ms.date: "09/20/2016"
ms.prod: "configuration-manager"
ms.reviewer: ""
ms.suite: ""
ms.technology:
  - "configmgr-other"
ms.tgt_pltfrm: ""
ms.topic: "article"
applies_to:
  - "System Center Configuration Manager (current branch)"
ms.assetid: 0caaf1d2-ffce-45f4-ae0c-d4e0eea2de71
caps.latest.revision: 8
author: "shill-ms"
ms.author: "v-suhill"
manager: "mbaldwin"
---
# How to Create an Operating System Deployment Task Sequence Package
You create an operating system deployment task sequence, in System Center Configuration Manager, by creating an instance of the [SMS_TaskSequencePackage](assetId:///SMS_TaskSequencePackage?qualifyHint=False&autoUpgrade=True) class. This class derives from the [SMS_Package](assetId:///SMS_Package?qualifyHint=False&autoUpgrade=True) class and holds the task sequence. It is advertised to clients who can then run the task sequence. The task sequence is associated with the task sequence package by using the assetId:///SMS_TaskSequencePackage?qualifyHint=False&autoUpgrade=True class [SetSequence](assetId:///SetSequence?qualifyHint=False&autoUpgrade=True) method.  

 You can organize task sequence packages into categories by assigning a category to them with the [SMS_TaskSequence](assetId:///SMS_TaskSequence?qualifyHint=False&autoUpgrade=True) class [Category](assetId:///Category?qualifyHint=False&autoUpgrade=True) property.  

 For more information about creating task sequences, see [How to Create a Task Sequence](../../develop/osd/how-to-create-an-operating-system-deployment-task-sequence.md). For more information about task sequence packages, see the [Task Sequencing Object Model](../../develop/osd/operating-system-deployment-task-sequence-object-model.md).  

 You advertise a task sequence package in the same way that you advertise a System Center Configuration Manager package (assetId:///SMS_Package?qualifyHint=False&autoUpgrade=True). For more information, see [How to Create an Advertisement](../../develop/core/servers/configure/how-to-create-an-advertisement.md).  

### To create a task sequence package  

1.  Set up a connection to the SMS Provider. For more information, see [About the SMS Provider in Configuration Manager](../../develop/core/understand/how-to-connect-to-an-sms-provider-by-using-managed-code.md).  

2.  Create an instance of assetId:///SMS_TaskSequencePackage?qualifyHint=False&autoUpgrade=True.  

3.  Populate the task sequence package properties.  

4.  Call the assetId:///SMS_TaskSequencePackage?qualifyHint=False&autoUpgrade=True class assetId:///SetSequence?qualifyHint=False&autoUpgrade=True method to associate a task sequence (assetId:///SMS_TaskSequence?qualifyHint=False&autoUpgrade=True) with the task sequence package.  

## Example  
 The following example method creates a task sequence package (assetId:///SMS_TaskSequencePackage?qualifyHint=False&autoUpgrade=True) and associates task sequence (assetId:///SMS_TaskSequence?qualifyHint=False&autoUpgrade=True) with it.  

 For information about calling the sample code, see [Calling Configuration Manager Code Snippets](../../develop/core/understand/calling-code-snippets.md).  

```vbs  
Sub CreateTaskSequencePackage (connection, taskSequence)  

    Dim taskSequencePackage  
    Dim packageClass  
    Dim objInParams  
    Dim objOutParams  

    ' Create the new package object.  
    Set taskSequencePackage = connection.Get("SMS_TaskSequencePackage").SpawnInstance_  

    ' Populate the new package properties.  
    taskSequencePackage.Name = "New task sequence package"  
    taskSequencePackage.Description = "A new task sequence package description"  

    ' Get the parameters object.  
    Set packageClass = connection.Get("SMS_TaskSequencePackage")  

    Set objInParams = packageClass.Methods_("SetSequence"). _  
        inParameters.SpawnInstance_()  

    ' Add the input parameters.  
    objInParams.TaskSequence =  taskSequence  
    objInParams.TaskSequencePackage = taskSequencePackage  

    ' Add the sequence.  
     Set objOutParams = connection.ExecMethod("SMS_TaskSequencePackage", "SetSequence", objInParams)  

End Sub  

```  

```c#  
public IResultObject CreateTaskSequencePackage(  
    WqlConnectionManager connection,   
    IResultObject taskSequence)  
{  
    try  
    {  
        Dictionary<string, object> inParams = new Dictionary<string, object>();  

        // Create the new task sequence package.  
        IResultObject taskSequencePackage = connection.CreateInstance("SMS_TaskSequencePackage");  

        taskSequencePackage["Name"].StringValue = "New task sequence package";  
        taskSequencePackage["Description"].StringValue = "A brand new task sequence package";  
        taskSequencePackage["Category"].StringValue = "A custom category";  

        // Note. Add other package properties as required.  

        // Set up parameters that associate the task sequence with the package.  
        inParams.Add("TaskSequence", taskSequence);  
        inParams.Add("TaskSequencePackage", taskSequencePackage);  

        // Associate the task sequence with the package. Note that a call to Put is not required.  
        IResultObject result = connection.ExecuteMethod("SMS_TaskSequencePackage", "SetSequence", inParams);  

        // The path to the new package.  
        Console.WriteLine(result["SavedTaskSequencePackagePath"].StringValue);  

        return taskSequencePackage;  
    }  
    catch (SmsException e)  
    {  
        Console.WriteLine("Failed to create Task Sequence: " + e.Message);  
        throw;  
    }  
}  

```  

 This example method has the following parameters:  

|Parameter|Type|Description|  
|---------------|----------|-----------------|  
|`connection`|-   Managed: [WqlConnectionManager](assetId:///WqlConnectionManager?qualifyHint=False&autoUpgrade=True)<br />-   VBScript: [SWbemServices](assetId:///SWbemServices?qualifyHint=False&autoUpgrade=True)|A valid connection to the SMS Provider.|  
|`taskSequence`|-   Managed: [IResultObject](assetId:///IResultObject?qualifyHint=False&autoUpgrade=True)<br />-   VBScript: [SWbemObject](assetId:///SWbemObject?qualifyHint=False&autoUpgrade=True)|A valid task sequence (assetId:///SMS_TaskSequence?qualifyHint=False&autoUpgrade=True).|  

## Compiling the Code  
 The C# example requires:  

### Namespaces  
 System  

 System.Collections.Generic  

 System.Text  

 Microsoft.ConfigurationManagement.ManagementProvider  

 Microsoft.ConfigurationManagement.ManagementProvider.WqlQueryEngine  

### Assembly  
 microsoft.configurationmanagement.managementprovider  

 adminui.wqlqueryengine  

## Robust Programming  
 For more information about error handling, see [About Configuration Manager Errors](../../develop/core/understand/about-configuration-manager-errors.md).  

## .NET Framework Security  
 For more information about securing Configuration Manager applications, see [Securing Configuration Manager Applications](../../develop/core/understand/securing-configuration-manager-applications.md).  

## See Also  
 [Configuration Manager Operating System Deployment](../../develop/osd/operating-system-deployment.md)   
 [Configuration Manager Objects](../../develop/core/understand/configuration-manager-objects.md)   
 [Configuration Manager Programming Fundamentals](../../develop/core/understand/configuration-manager-programming-fundamentals.md)   
 [How to Connect to an SMS Provider in Configuration Manager by Using Managed Code](../../develop/core/understand/how-to-connect-to-an-sms-provider-by-using-managed-code.md)   
 [How to Connect to an SMS Provider in Configuration Manager by Using WMI](../../develop/core/understand/how-to-connect-to-an-sms-provider-in-configuration-manager-by-using-wmi.md)   
 [How to Create a Task Sequence](../../develop/osd/how-to-create-an-operating-system-deployment-task-sequence.md)   
 [Operating System Deployment Task Sequencing](../../develop/osd/operating-system-deployment-task-sequencing.md)