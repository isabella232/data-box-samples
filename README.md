# export-xml-gen
`generateXMLFilesForExport.ps1` generates xml files for exporting blob storage containers to multiple DataBoxes or DataBox Heavies. For more information about making an export order using XML files see [Export order using XML file](https://docs.microsoft.com/en-us/azure/databox/data-box-deploy-export-ordered?tabs=sample-xml-file#export-order-using-xml-file)


## Requirements
- Powershell 5.1 or higher
- Az Powershell 6.4.0. [installation guide](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-6.4.0) 

## Syntax
Split By Device
```
.\generateXMLFilesForExport.ps1
        [-Subscription] <String>
        [-ResourceGroupName] <String>
        [-StorageAccountName] <String>
        [-Device] <String>
        [-ContainerNames] <String[]> (Optional)
        [-StorageAccountKey] <String> (Optional)
```
Split By DataSize
```
.\generateXMLFilesForExport.ps1
        [-Subscription] <String>
        [-ResourceGroupName] <String>
        [-StorageAccountName] <String>
        [-DataSize] <Long>
        [-ContainerNames] <String[]> (Optional)
        [-StorageAccountKey] <String> (Optional)

```

## Parameters
#### `Subscription <String>`
- Name of Subscription
#### `ResourceGroupName <String>`
- Name of Resource Group
#### `StorageAccountName <String>`
- Name of Storage Account
#### `Device <String>`
- Device you are exporting to. Valid options are "DataBox" and "DataBoxHeavy"
#### `ContainerNames <String[]>`
- Names of containers you want to export. This parameter supports one container, a list of containers separated by commas, and wildcard characters. If this parameter is not specified, all containers in the storage account will be processed. 
#### `StorageAccountKey <String>`
- Access key for the storage account.
#### `DataSize <Long>`
- Size of the device you are exporting to. This parameter is mostly for testing and you probably don't need it. 
## Run the script and make export orders

1. Open PowerShell as Administrator.
2. Set your execution policy to **Unrestricted**. This is needed because the script is an unsigned script.

   ```azurepowershell
   Set-ExecutionPolicy Unrestricted
   ```

4. Run the script. For example:  

    ```
    .\generateXMLFilesForExport.ps1 -Subscription exampleSub -ResourceGroupName exampleRG -StorageAccountName exampleStorageAccount -ContainerNames container1,container2 -Device DataBox
    ```

5. With an **Unrestricted** execution policy, you'll see the following text. Type `R` to run the script.

   ```azurepowershell
   Security warning
   Run only scripts that you trust. While scripts from the internet can be useful, this script can potentially harm your computer.
   If you trust this script, use the Unblock-File cmdlet to allow the script to run without this warning message. Do you want to
   run C:\scripts\generateXMLFilesForExport.ps1?
   [D] Do not run  [R] Run once  [S] Suspend  [?] Help (default is "D"): R
   ```

6. Make export orders.  
   When the script completes all the export xml files will be in the folder `exportxmlfiles`.  
   Follow the instructions on [Export order using XML file](https://docs.microsoft.com/en-us/azure/databox/data-box-deploy-export-ordered?tabs=sample-xml-file#export-order-using-xml-file) to create an export order for each export xml file. 

## Exporting a container with churning data
To minimize risk, avoid running this script on containers with churning data. If that is not possible, here is some important information about this script's behavior on churning data. 
1. All blobs present in the storage account when the script is ran will be included in the export xmls if there are no deletions
2. Blobs added to the storage account after the script is ran may or may not be included in the export xmls
3. Deletion after the script is ran may result in script failures or export order failures

##  Script Performance

This script's performance is bottlenecked by the number of blobs you want to export. If you are exporting containers with >100 million blobs, consider running this script on an Azure VM located in the same datacenter as the containers. This script processes 1 million blobs in ~2.5 mins running on an Azure VM and can take >5 mins per 1 million blobs on a local machine depending on network speed. See [Azure VM Documentation](https://azure.microsoft.com/en-us/services/virtual-machines/#overview).