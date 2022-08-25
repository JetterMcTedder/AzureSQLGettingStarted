# Azure SQL Database Import Data Samples for Developers and DBAs

#### Version 1.0 (2022-08-25)

## Pre-Requisites

### Create a **free** Azure Accout

[FREE Azure Account](https://azure.microsoft.com/en-us/free/sql-on-azure/)

### Create an Azure SQL Database

Create a database in Azure that you can use for these data loading samples. 

If you are new to Azure SQL and need help in creating a new database, make sure to watch this 5 minutes video:

[Demo: Deploy Azure SQL Database](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Demo-Deploy-Azure-SQL-Database-14-of-61)

[Quickstart: Create a single database – Azure SQL Database](https://docs.microsoft.com/en-us/azure/azure-sql/database/single-database-create-quickstart?view=azuresql&tabs=azure-portal)

### Create an Azure Storage Account

An Azure storage account contains all of your Azure Storage data objects: blobs, files, queues, and tables. This is where we will be placing out sample files for data loading.

[Create a storage account](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal)

[Quickstart: Upload, download, and list blobs with the Azure portal](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal)

### Download and Install Azure Data Studio

Azure Data Studio is a cross-platform database tool for data professionals who use on-premises and cloud data platforms on Windows, macOS, and Linux.

[Azure Data Studio Download](https://docs.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio)

## Data Loading in Azure SQL

### Create the table

```
create table [dbo].[bad_pets]
(
    id int not null,
    pet_type varchar(100),
    reason varchar(400)
);
```

### Create the Secret and Credentials
```
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'SuperSecretPassword';
CREATE DATABASE SCOPED CREDENTIAL AzureBlobStorageCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
SECRET = 'SharedAccesstoken';
```

### Create external data source
```
CREATE EXTERNAL DATA SOURCE ExternalAzureBlobStorage
WITH (	TYPE = BLOB_STORAGE, 
	LOCATION = 'https://STORAGE_ACCOUNT_NAME.blob.core.windows.net/CONTAINER_NAME',
        CREDENTIAL = AzureBlobStorageCredential);
```

### Bulk load the data
```
BULK INSERT [dbo].[bad_pets]
FROM 'bad_pets.csv'
WITH (DATA_SOURCE = 'ExternalAzureBlobStorage',
FORMAT = 'CSV',
FIRSTROW = 2);
```