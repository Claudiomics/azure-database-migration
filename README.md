# Azure Database Migration Project

![Microsoft](https://img.shields.io/badge/Microsoft-0078D4?style=for-the-badge&logo=microsoft&logoColor=white)
![Azure](https://img.shields.io/badge/azure-%230072C6.svg?style=for-the-badge&logo=microsoftazure&logoColor=white)
![MicrosoftSQLServer](https://img.shields.io/badge/Microsoft%20SQL%20Server-CC2927?style=for-the-badge&logo=microsoft%20sql%20server&logoColor=white)

# Table of Contents
1. [Introduction](#introduction)
2. [Production Environment Setup](#production-environment-setup)
   1. [Provisioning the Windows Virtual Machine](#provisioning-the-windows-virtual-machine)
   2. [SQL Server and SSMS](#sql-server-and-ssms)
   3. [Creating the Production Database](#creating-the-production-database)
4. [Azure SQL Database Migration](#azure-sql-database-migration)
   1. [Setting Up Azure SQL Database](setting-up-azure-sql-database)
   2. [Preparing for Migration](#preparing-for-migration)
   3. [Schema Migration](#schema-migration)
   4. [Data Migration](#data-migration)
5. [Data Backup and Restore](#data-backup-and-restore)
   1. [Backing up the On-Premise Database](#backing-up-the-on-premise-database)
   2. [Uploading to Blob Storage](#uploading-to-blob-storage)
   3. [Restoring the Database on Development Environment](#restoring-the-database-on-development-environment)
   4. [Automating Backups for Development Database](automating-backups-for-development-database)
6. [Disaster Recovery Simulation](#disaster-recovery-simulation)
   1. [Mimicing Data Loss in Production Environment](#mimicing-data-loss-in-production-environment)
   2. [Restoring Database from Azure SQL Database Backup](#restoring-database-from-azure-sql-database-backup)
7. [Geo Repliacation and Failover](#geo-replication-and-failover)
   1. [Setting up Geo-Replication for Azure SQL Database](#setting-up-geo-replication-for-azure-sql-database)
   2. [Testing Failover and Failback](#testing-failover-and-failback)
8. [Microsoft Entra Directory Integration](#microsoft-entra-directory-integration)
   1. [Configuring Microsoft Entra ID for Azure SQL Database](#configuring-microsoft-entra-id-for-azure-sql-database)
   2. [Creating a Database Reader User](#creating-a-database-reader-user)
9. [Licence Information](#licence-information)

## Introduction 

Welcome to my Azure Database Migration Project!

This README.md outlines the process of setting up a cloud-based database system on Microsoft Azure, showcasing my skills in this cloud-based technology. This project entails creating an Azure Virtual Machine (VM) and restoring a database before migrating the database and simulating a disataer recovery for data loss. 

I have included a brief description and analysis of each section before delving into more detail with the step-by-step process I took to achieve this project.

Here is a brief overview of the entire process:

<img width="847" alt="Screenshot 2024-03-17 at 23 45 14" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/d837fe6e-0d1a-4d0c-90f5-171aa97287ab">

## Production Environment Setup

In a production environment, a database can be established and made available for client presentation. On the other hand, the development environment serves as an isolated space where developers can test and create new features which are only transferred to the production environment after testing to ensure functionality and prevent accidental data loss.

In the following steps, I will outline the creation of a production environment to restore the Microsoft SQL Server 'AdventureWorks' production database, which is a sample database provided by Microsoft which emulates a fictional manufacturing company's operations, effecitvely replicating a real-world production database scenario. I've chosen the Windows 11 Pro operating system as my virtual machine as this setup simulates a secure and dedicated data storage solution that is likely what an on-premise system within a company would use for this type of database manipluation. Running a Windows 11 VM on my local machine offers several advantages such as being able to access exclusive windows applications and software, and being able to run this software without the need for a seperate physical windows machine. To enable incoming network traffic from my local port specifically when creating my virtual machine, I needed to create an inport bound rule which would allow me to connect through the Remote Desktop Protocol (RDP), as all inbound traffic is blocked for security purposes by default.

I hosted my database on Azure SQL as it is a cloud-based relational database service providing a managed and scalable platform for running SQL Server databases on the cloud. Azure SQL is built on SQL Server technology, a widely-used relational database management system (RDBMS). It offers many of the same features and functionality as SQL Server but with the added benefits of cloud computing, as well as enhanced performance, scalability and security. I needed to install SQL Server and SQL Server Management Studio (SSMS) onto my VM to manage the AdventureWorks database. __SQL Server__ is a relational database mamagement system (RDBMS) developed by Microsoft to store and manage vast quantities of data and supports structured query language (SQL). __SQL Server Management Studio (SSMS)__ is a powerful graphical user interface (GUI) tool for managing SQL Server instances and databases. It is a central hub for database administrators and developers to perform various tasks related to SQL Server. I downloaded the database onto SQL Server using SSMS's backup option after downloading the .bak file.

The following outlines this process in a step-by-step manner:

[Back to Table of Contents](#table-of-contents)

[Skip to Azure SQL Database Migration](#Azure-SQL-Database-Migration)

### Provisioning the Windows Virtual Machine

1. I created a Microsoft Azure Account from my local machine's browser.
2. Within the Microsoft Azure portal's home page, I navigated to the `Virtual Machines` page > `+ Create` > `Azure Virtual Machine` to start the process.
4. The following page required the addition of several key pieces of information:
     1. `Subscription` - I chose which account subscription I wanted the VM to be managed and billed by.
     2. `Resource Group` - I created a new resource group.
     3. `Name` - I named my VM something descriptive.
     4. `Region` - I chose the region I was geographically located in.
     5. `Image` - The operating system I chose was Windows 11 Pro. 
     6. `Size` - I chose the B2ms general purpose VM as I don't need massive storage space and has a low running cost.
     7. `Administrator account` - I created a password-based authentication which will be used to log into the VM's operating system.
     8. `Inbound port rules` - To enable remote access to the VM through Remote Desktop Protocol (RDP), the RDP port (port 3389) was allowed. 
5. After pressing `Review + Create` and then `Create`, I was able to view my resource in the portal, as shown below:

<img width="900" alt="Screenshot 2024-01-28 at 20 33 46" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/fc36be35-885c-44c7-9d45-5589859aa6a5">

8. To connect to this VM, I downloaded the .RDP file from the VM Overview page (`Connect` > `Download RDP file`) before dragging this file into the `Microsoft Remote Desktop Client` app on my local machine.
<img width="900" alt="Screenshot 2024-01-28 at 20 35 09" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/a13ad959-6b94-4e80-9508-7ee190d07296">

10. I double clicked my VM and used my credentials to log in.
<img width="900" alt="Screenshot 2024-01-28 at 20 36 40" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/cfc9a97c-9e7f-48b3-8dcb-7002bf3a41ea">

### SQL Server and SSMS

1. I downloaded `SQL Server Installer` from the Microsoft Download Centre on my VM's browser.
2. I ran the SQL Server `Installation Wizard` and chose the `Basic` option.
3. I accpeted the license terms and chose where to install it to on my VM.
4. After it was finished installing, it prompted me to download SQL Server Management Studio (SSMS).
5. After following the installation process, I connected to the SQL Server using SSMS by using 'localhost' as the server name (as the database is on the same machine).
7. I used `Windows Authentication` and my VM's Windows credentials to log in > `Connect`. I could then see my SQL Server instance displayed under the Object Explorer:

<img width="900" alt="Screenshot 2024-01-28 at 22 22 59" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/38d58d8e-1d78-4f9b-885b-0465c8e3e83d">

### Creating the Production Database

1.	Firstly, I downloaded the Production Database AdventureWorks from the file provided by Mcirosoft onto my VM. (link https://aicore-portal-public-prod-307050600709.s3.eu-west-1.amazonaws.com/project-files/93dd5a0c-212d-48eb-ad51-df521a9b4e9c/AdventureWorks2022.bak )
2.	I copied the file and put it into ` C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup` on the VM.
3.	With the SQL Server connection active on SSMS, I right-clicked on `Databases` in Object Explorer and chose `Restore Database`.
4.	For the source, I choose `Device`, `…` and `Add` to select the .bak backup file that’s in the Backup folder. 
5.	On the General page, I made sure the database was correct and selected `OK`, which came up with a `Database restored successfully` message.
   <img width="900" alt="Screenshot 2024-01-28 at 22 29 24" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/8f6cfa4f-16ef-4850-aa8b-d61fae999414">

7.	I right clicked the database and made a quick query to make sure everything was working alright.

<img width="900" alt="Screenshot 2024-01-28 at 22 31 21" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/7271b79f-078c-4661-9eca-27dbdb3449e0">

## Azure SQL Database Migration 

Azure SQL Database is an online database service (Platform-as-a-Service) that has high availability and disaster recovery capabilities, providing automated backups to protect data from loss or corruption and to minimise downtime, with the option to change how frequently your data is backed up and how long each backup is retained. It's security is also advanced, with it supporting Microsoft Entra ID authentication and role-based access control as well as a built-in firewall restricting access depending on IP addresses or ranges. Hosting SQL databases on the cloud elimiates the need for on-oremises hardware and maintenance.

To transition a database to Azure's cloud, it must be migrated from the on-premise database to Azure SQL Database, which serves as the migration target. Initially, I created an Azure SQL Database server within the Azure Portal which acted as a container for the database and promts you to set up SQL Server Authentication so one can log in from SQL Server.

To facilitate the migration of my on-premise database, I used the powerful database managemet tool developed by Microsoft - `Azure Data Studio` (ADS). ADS can be used to manage various data platforms including Azure SQL Database, SQL Server PostgreSQL and MySqL, among others. It it a powerful tool which simplifies database management tasks.

Prior to migration, firewall rules and settings needed to be configured to allow connections to Azure SQL Database as, by default, it is configured to deny all public connections. To use Azure Data Studio for the local SQL Server database, a connected was established. Then to be able to move the data onto Azure SQL Database, this first needed to be created in the portal and then the IP address from the virtual machine added to the firewall rules to allow the successful connection from Azure Data Studio to the Azure SQL Database using SQL Server Authentication. The schema was then migrated using SQL Server Schema Compare extension, followed by the data using the Azure SQL Migration extension. To ensure data was migrated properly, SQL queries were made to check the data was transferred fully.

[Back to Table of Contents](#table-of-contents)

[Skip to Backing up the On-Premise Database](#Backing-up-the-On-Premise-Database)

### Setting Up Azure SQL Database

1. In the Azure Portal, I navigated to `SQL Database` > `Create`.
2. I created a new resource group 'adventureworks-rg, and named the database 'AdventureWorks2022'.
3. I created a new server with the name 'ad-works-server', geographic location UK South and SQL Server Authentication username and password. I configured compute to the `Basic` storage plan.
4. I clicked `Review + Create` > `Create` before navigating to the resource to add some firewall rules.

<img width="800" alt="Screenshot 2024-02-01 at 14 53 57" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/4aa98ed5-d505-4038-a9d4-09f476e76625">

5. I clicked `Set Server Firewall` > `Selected Network` and pressed `Save`.

<img width="900" alt="Screenshot 2024-02-01 at 15 24 17" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/3fa443e1-814d-4dbc-bf54-0bed792137f0">

6. To connect to this Azure SQL Database, VSCode was used on my local machine.
7. I installed the VSCode `SQL Server` extension and used it to `+ Connection`.
8. This prompts the entry of `Server Name`,`DB Name`, `SQL Login details` and `Display name`, however this prompted me to `Add account` so I signed into Azure using my Account details.
<img width="469" alt="Screenshot 2024-02-01 at 15 33 02" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/e4380905-fc92-4d32-ac19-6d5d5092fc2f">
9. I repeated the VSCode `+ Connection` steps again, and it prompted me to add a firewall rule. I clicked on the promt and it autofilled my local machine's IP address into the 'Start' and 'Finish' points.
    
<img width="464" alt="Screenshot 2024-02-01 at 15 35 55" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/38f0c136-b514-46c6-a444-1f949095dcbb">

<img width="635" alt="Screenshot 2024-02-01 at 15 38 01" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/b75d2398-35a5-4d4d-8e8c-9228421c1c65">

11. Once again, I pressed `+ Connection` on VSCode and was able to connect successfully to the Azure SQL Database.

<img width="258" alt="Screenshot 2024-02-01 at 15 41 35" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/4219ac76-7251-49a5-8243-cf3a866b6c03">

### Preparing for Migration

1. To connect the on-premise database on my VM tto Azure Data Studio, I downloaded the Windows version of `Azure Data Studio`. I installed the programme and clicked `Create a desctop icon` to be able to access the app quickly.
2. I launched `Azure Data Studio` and clicked `New Connection` and `Microsoft SQL Server` as the Server Type.
  
<img width="1023" alt="Screenshot 2024-02-01 at 15 43 17" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/6c42c41e-7409-4fb8-a28e-fe190eaab6e8">

4. I clicked `Enable Trust Server Certificate` and used 'localhost' as the server name, with Windows Azure authentication type for authentication.
5. I selected AdventureWorks2022 as the database > `Connect` > `Enable Trust Certificate`.
6. I then connected to the Azure SQL Database by clicking `Server` within Azure Data Studio and selected `New Connection`.
7. I selected `Miscrosoft SQL Server` as the server type and entered the connection details like Server Name and SQL Login details that were set up when creating the Azure SQL Server in the Azure Portal. I selected the database from the dropdown menu and clicked `Connect` but was met with an error message explaining the virtual machine can't access the SQL Server as the VM IP Address wasn't added to the SQL Server firewall.
8. To overcome this, I navigated to the Virtual Machines service page on the Portal and copied the `Public IP address` from the Overview page.
9. I then went to the SQL Servers page and clicked on the one I wanted to host my Azure SQL Database on, before clicking on the `Networking` subsection on the left hand side of the page.
10. I selected `Add a firewall rule` and pasted the virtual machine IP address on the `Start IP` and `End IP` fields and then saved my changes. This allowed the VM to connect to the SQL server.
11. I went back to Azure Data Studio and reconnected to the SQL Server and this time the connection was a success.

### Schema Migration

1. The `SQL Server Schema Compare` Azure Data Studio extension was installed.
2. I clicked on the Servers icon, right clicked the local server and selected `Schema Compare`.
3. I configured the source connection to the local SQL Server database and target connection to Azure SQL Database > `OK`.
4. I clicked `Compare` at the top of the page and chose to migrate all the schema across to the Azure SQL Database Server.
 
<img width="1201" alt="Screenshot 2024-02-03 at 18 06 30" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/4cd74062-5fb3-4f85-a6e2-03794d2c2cf1">

5. I applied all the changes and the schema was visible in the Azure SQL database server after I refreshed the `Tables` node. However since the data hadn't been migrated yet, the SQL Query 'SELECT TOP 1000' showed empty.

### Data Migration 

1. On Azure Data Studio I installed the `Azure SQL Migration` extension.
2. I right clicked on the local SQL Server database and selected `Manage`.
3. I selected `Azure SQL Migration` under `General` and clicked `Migrate to Azure SQL`.
4. The Azure SQL Database was selected as the target and confirmed the database to be migrated.
5. To configure the Azure SQL target, login credentials were required and the right target database selected.
6. An Azure Database Migration Service was required to configure the database migration, so this was set up before proceeding. I went to the Azure Portal and searched `Azure Database Migration Services` and clicked `Create`.
7. To finish setting up this resource, I used the same recource group as my VM and the same geographical location before selecting `Create`.
8. Back on Azure Data Studio, the Azure Database Migration Service had to be registered, so I downloaded and installed the latest version of `Integration Runtime` from the provided link.
9. To finish registration, I copied and pasted one of the 'keys' provided by Azure Data Studio and clicked `Launch Configurtion Manager` and `Next`.
10. I selected the tables to migrate (all) and clicked `Update`.
11. I clicked `Run validation` which ensured the settings were correct and identifies any potential issues for the actual migration process.

<img width="1195" alt="Screenshot 2024-02-03 at 18 08 28" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/40e34d32-55c4-4608-a7b3-44bcee5fb63d">

12. My check showed there were no errors so I pressed `Start Migration`.

<img width="1395" alt="Screenshot 2024-02-03 at 18 09 48" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/89373759-622d-4aff-bc46-69d932754f07">

13. To check there was data in the tables after the migration status read complete, I used `Select Top 1000` which showed the data within the tables.
14. I also checked the Azure Portal to see my database was successfully in the cloud.

## Data Backup and Restore

Before creating a development environment for this database, I had to ensure the stored data in the production database was secure. The production database is for storing and presenting real customer data and the development database is for experimental testing. By provisioning a development database, it ensures there will be no accidental data loss or corruption to the production database, effectively maintaining the integrity of the live data.

The current Windows VM holds the production environemnt and database and this needed to be backed up in a way that would allow database restoration in a development environment. I achieved this by generating a full backup of the database (capturing the entire database at a specific point) and storiing in a backup file on the Virtual Machine, before transferring it to Azure Blob storage. I then configred a second virtual machine as the development environement before restoring the database from the blob onto this second VM and configured the setting to run automatic backups. 

This system allows the quick restoration of data in the case of data loss or corruption due to unexpected events of cyber attacks, with minimum downtime. Having this system in place with regular backups minimises any negative implications to organisations facing data loss issues.

The following steps were taken to achieve this:

[Back to Table of Contents](#table-of-contents)
[Skip to Disaster Recovery Simulation](#disaster-recovery-simulation)

### Backing up the On-Premise Database

1. On the production VM, I opened SSMS and connected to the SQL Server Instance which was hosting the on-premise database.
3. I right clicked on the database I wanted to backup and selected > `Tasks` > `Back-up`.
4. In the dialogue box that popped up, I chose a name for the backup file and selected the destination for it to be saved in as the following filepath: `C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\`.
5. I chose the `full` backup type and clicked `OK` to create the backup process.
6. I recieved the message "The backup of database 'AdventureWorks2022' Completed Successfully".

### Uploading to Blob Storage

1. First, I set up a storage account and container by searching `Storage Accounts` and clicking `Create` in the Azure Portal. 
4. I accessed the storage account and clicked `Upload` before dragging and dropping the on-premise database from the `\Backup\` location.
5. I selected the container I wanted to store the database in and waited for it to upload.

### Restoring the Database on Development Environment

1. I provisioned another Azure Windows VM to use as a development evnironment, following the [same method as before](#Provisioning-the-Windows-Virtual-Machine), and named my machine `development-vm`, before connecting to it using Microsoft Remote Desktop.
2. I connected to the machine, logged onto Microsoft Azure and downloaded the `` file from Blob storage. I copied this file from where it was automatically saved to the filepath `f` so I could use it for creating the backup.
3. On my development-vm, I installed and downloaded SQL Server Management as shown [before](#SQL-Server-and-SSMS), and opened it.
4. I connected to the server and right clicked the database > `Restore` > `Device`, selected the .bak file from the backup folder and clicked `Add`.
<img width="378" alt="Screenshot 2024-03-01 at 17 05 12" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/6da907c5-a9bc-4e2a-90f9-12cdaac07195">
<img width="989" alt="Screenshot 2024-03-01 at 16 24 04" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/11c74607-8f08-41db-bac7-6c41e739e0d8">
<img width="1150" alt="Screenshot 2024-03-01 at 16 24 28" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/e57e7c5c-ef17-440d-8243-db425fd4d227">

### Automating Backups for Development Database

1. On my development virtual machine, in SSMS, I right clicked on `SQL Server Agent` and clicked `Start` > `Yes`, which creates a drop-down menu for this subunit.
2. Before continuing, I created an SQL Server Credential in SSMS which is required to enable access to an Azure Storage account where the database will be backed up to.
3. I right clicked on the server name and selected `Create Query`, and typed in the following before pressing `Execute` to create the credential. I got the Storage Account name and Access Key from my Azure Storgae Account under `Security and Networking` > `Access Keys`.

`
CREATE QUERY Claudia_Creds
WITH IDENTITY = '[Azure Storage Account Name]'
SECRET = '[Access Key]'`

5. I refreshed the Object Explorer and checked the credential was visible in the `Security` segment of the server. 
6. I pressed the `+` button of the `Management` node, right clicked `Maintenance Plans` and selected `Maintenance Plan Wizard`.
7. I entered a descriptive name for the backup plan and pressed `Next`. I left the backup schedule as default for now to test the plan before committing to a set backup timescale. 
8. I selected `Back Up Database (Full)` and pressed `Next`.
9. In the `Back Up Database Task` segment, I selected the specific database I wanted to upload and chose `URL` as a backup destination.
10. Under the `Destination` tab, I selected the SQL Server Credential I created in step 3 and under `Azure Storage Container` I entered the name of the container in Azure I want to upload the database to.
11. I clicked through `Next` until I reached the end and clicked `Finish` to create the maintenance plan.
13. I refreshed the maintenance plan node, right clicked on my newly created plan and clicked `Execute`.
14. I checked my Storage Account on Microsoft Azure to ensure the plan was working as expected and returned to SSMS.
15. I modified the backup schedule by right-clicking on the plan name, selecting `Modify` before setting the schedule by clicking the `Calender` icon which opened up a `New Job Schedule` window and I selected weekly backups.

## Disaster Recovery Simulation

Microsoft Azure has great disaster recovery capabilities to minimize downtime, protect data, ensure compliance and adapt to cyber security threats.

This section of the project focuses on simulating the loss of data in a production environemnt, showing the process of being able to recover the lost or corrupt data from the development environment. In a real-life setting, this testing would be completed using the development environment, as deleting any live data would be really risky and could have a negative impact if the test didn't work. This simulation is using my initial produciton environment VM as it simulates the real-life data loss scenario and the data isn't critical. I noted down the exact data that I corrupted the data so I could set the recovery date and time to a time the loss definietly occured before.

It's improtant to documentation this process so the exact steps can be followed to reverse any negative outcomes from real-life incidents and minimize the impact on critical business operations. Additioanlly, communicating to stakeholders when testing is crucial to build trust and prevent any confusion. 

The following segments outline the steps I took:

[Back to Table of Contents](#table-of-contents)

[Skip to Geo Repliacation and Failover](#geo-replication-and-failover)

### Mimicing Data Loss in Production Environment

1. On my production virtual machine (claudias-vm), I went onto Azure Data Studio and right clicked on my Azure SQL Database and created a new query to view some data in the ad-works database. This allowed me to examine the data before I chose what to delete. I pressed 'Run' to execute the query and the result showed there were 1596 entries within this table.
`SELECT * FROM dbo.DatabaseLog;`

<img width="1382" alt="Screenshot 2024-03-07 at 16 39 40" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/6605c7cd-b8aa-49f6-85aa-149230536d51">

3. I ran a new query to delete the first 96 from the dbo.DatabaseLog table.
`DELETE TOP (96) FROM dbo.DatabaseLog;`

5. To ensure the data was deleted, I re-ran the initial query and saw there were now 1500 entries, showing the data deletion was successful.
<img width="1088" alt="Screenshot 2024-03-07 at 16 43 25" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/7489186a-461a-4a88-80d0-5e079a75c163">

### Restoring Database from Azure SQL Database Backup

1. In the Azure Portal on my production vm, I navigated to `Azure SQL Database` and selected the target database that I wanted to restore and pressed `Restore`.

<img width="778" alt="Screenshot 2024-03-07 at 16 51 31" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/76c6ceb0-a844-4467-93e1-768471536182">

2. I selected a point in time from which to restore the database from, from when I knew the database wasn't corrupted. I chose the previous day, however in a real-world situation with an active database, I would choose a point that is as close to the point where data loss had occrued as possible.
3. I entered a new Database name which is simply the name of the original database with `-restored` added to the end, before clicking `Review + create` and `Create`.
4. To ensure the success of the restoration, I established a new connection via Azure Data Studio and used a query to see the number of entries in the dbo.DatabaseLog table in the -restored database.
<img width="1389" alt="Screenshot 2024-03-07 at 17 31 32" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/540b4001-687d-410e-a6ff-a325e34b66e7">
6. The table shows the original number of 1596 entries, showing that the restoration was a success.

## Geo Repliacation and Failover

One feature in Azure SQL Database is Geo-Replication, which asynchronously replicates a chosen database to a secondary region. This is a service that provides data redundancy and high availability in the case of a regional outage or disaster, and the database can be restored from the second geographical location. The database in the primary location is the original, and handles read and write operations, whereas the database in the secondary location is a read-only copy. Failover is the process of switching the workload from the primary to secondary region and a tailback is the reverse process where the workload is reverted back to the primary region.

This section of the project configures the geo-replication of the database, increading data protection by establishing a copy of the database in a secondary region. I set up the access setting in the next section of this project. I completed a failover test, connected to the server in the new geographical location from Azure Data Studio and completed an SQL Query to test the database works. I then switched it back to the primary region by preforming a tailback on the Azure Portal.

Below is the step-by-step process I took to complete this part of the project.

[Back to Table of Contents](#table-of-contents)

[Skip to Microsoft Entra Directory Integration](#microsoft-entra-directory-integration)

### Setting up Geo-Replication for Azure SQL Database

1. In the Azure Protal, I went to `Azure SQL Database` and selected the primary database that had been restored in the previous steps.
<img width="1440" alt="Screenshot 2024-03-08 at 14 23 37" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/fe82c493-68c1-46b9-afa5-f932277d7a2c">
2. I navigated to `Data Management` > `Replicas` and clicked `+ Create replica` to begin the process.
3. In the Geo Replica menu I first created a new SQL Server (claudia-rep-server) and selected a geographic region that is different from my inital one for the secondary database to be located (Europe) Norway East, before selecting `Use SQL authentication` to provision the log in credentials for this server and clicking `OK`.
4. Back in the original dialogue box, I pressed `Review + create` and `Create` to finalise the replication process. I clicked on `Go to Resource` to see the details and the 'type' showed it was 'Geo'. 
<img width="1093" alt="Screenshot 2024-03-08 at 14 55 44" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/048c23d9-413a-40f8-a114-4a7874418559">
<img width="1311" alt="Screenshot 2024-03-08 at 15 01 47" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/56b47398-e35c-435a-9aa2-e26c0279c42e">

### Testing Failover and Failback

1. In the Azure Portal again, I navigated to `Azure SQL Database` and selected the SQL server associated with the primary database I wanted to failover (the original, restored one, NOT the geo-replicated one).
2. Under `Data management` I selected `Failover Groups` and clicked `+ Add group`.
3. I created a new name 'adworks-failover' and for `Server` I chose the secondary location server 'claudia-rep-server', before clicking `Create`.
4. After deployment was complete, I navigated to the 'ad-works-server' and then `Failover groups` under `Data management` and clicked on the 'adworks-failover'.
5. This page showed a map of the world with the two servers shown as primary and secondary.
6. I initiated a planned failover by selecting `Failover` and waited till after this was complete to ensure the primary location was switched to Norway East.

<img width="1440" alt="Screenshot 2024-03-08 at 18 56 32" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/cd5cbeb9-6c32-4908-bc92-e9dc9c331eb1">

<img width="1440" alt="Screenshot 2024-03-08 at 18 56 18" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/5df64eec-1b24-43df-8b95-5922a621e697">

7. To test the database works fine in the switched server, I connected to the server through Azure Data Studio on my production vm by copying the endpoint and pasting it into ADS and inputting the credentials I made for the geo replication process. I pressed connect and queried the database to  check the database was working well.
7. As the failover was a success, I repeated the failover to switch the primary server back to UK South.

## Microsoft Entra Directory Integration

Microsoft Entra ID is a cloud-based identity and access management system which can be configured by the administrator account to manage users, groups and applications and this can be used to manage access to Azure SQL Database too. Features such as user authenication and access management help keep organisations' data secure and secures data from data modifications from unauthorised personel.

To manage who can access the data, I first created an admin account which holds the authority to manage and oversee the production database. Enabling Microsfot Entra ID authentication for the SQL Server hosting the production database was essential. Additionally, I ensured that Azure Data Studio could establish a connection using Microsoft Enterprise credentials. I then go on to provision database reader users with read-only access so the data can't be corrupted by individuals without clearance. 

The following steps outline this process in more details.

[Back to Table of Contents](#table-of-contents)

### Configuring Microsoft Entra ID for Azure SQL Database

1. Within the Azure Portal I opened the SQL Server hosting my primary database.
2. Under `Settings` I clicked on `Microsoft Entra`.

<img width="1433" alt="Screenshot 2024-03-19 at 23 50 58" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/280f51a9-e96f-4f4a-98d3-30364508b753">

4. I clicked `Set admin` to assign a Microsoft Entra user or group as the Microsoft Entra admin for the SQL Server and selected my account as the admin for the database and saved my changes.
 
 <img width="1438" alt="Screenshot 2024-03-19 at 23 54 40" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/f70c17d8-4b98-4593-9d05-3fddeead2c20">

5. Using Azure Data Studio on my VM, I disconnected from my this server and then re-connected using `Microsoft Entra ID- Universal with MFA support` instead of `SQL Login` like before.
7. I added my account by clicking `Add an account` which re-directed me to log in to Azure and then I could successfully connect.
 <img width="1398" alt="Screenshot 2024-03-20 at 00 02 17" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/ce044b60-5d53-4835-92d7-50eb70793fea">

9. I tested my read and write access by right clicking on one of the tables and selecting `Edit`. 
<img width="1390" alt="Screenshot 2024-03-20 at 00 03 43" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/45c2492d-8192-4bc6-be0c-21783b12b9de">

### Creating a Database Reader User

1. I created a new database reader user by navigating to `Microsoft Entra ID` homepage within the Azure Portal and selecting `+ Add`.
2. I named them '_DB_Reader' and used `Auto-generate password`.
3. I navigated to Azure Data Studio and ensured I was connected to the ad-works-server hosting my primary database and selected `New Query` within the target database.
4. I executed the following query to grant read-only access to the _DB_Reader user I just created.
`
CREATE USER [_DB_Reader@aicoreusers.onmicrosoft.com] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [_DB_Reader@aicoreusers.onmicrosoft.com]; 
`
<img width="1440" alt="Screenshot 2024-03-20 at 00 24 44" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/0c1dd55a-7958-49ca-b89b-6548300cf981">

5. I checked the access level by disconnecting and re-connecting to the server using the _DB_Reader user credentials (using `Microsoft Entra ID- Universal with MFA support` and click `Add Account` under `Accounts`).
6. I logged in using '_DB_Reader@aicoreusers.onmicrosoft.com' and the password provded. I had to download the `Microsoft Authenticator` app on my phone and verify myself before I was able to connect.
7. The following images show this user is able to view the data in the databases but can't edit them.
<img width="1440" alt="Screenshot 2024-03-20 at 00 39 21" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/3c41c5ed-dfdc-4688-8eca-ed21bad1ecfd">

<img width="1397" alt="Screenshot 2024-03-20 at 00 41 13" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/6c88b2d4-fd5a-46f3-b104-c48d4db1138f">

<img width="1399" alt="Screenshot 2024-03-20 at 00 43 14" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/57a968da-8c89-47a6-aa68-7a03cbd548cb">

[Back to Table of Contents](#table-of-contents)

### Conclusion


[Back to Table of Contents](#table-of-contents)

