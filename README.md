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

Welcome to my Multinational Retail Data Centralisation Project!


This README.md outlines the process of setting up a cloud-based database system on Microsoft Azure, showcasing my skills in this cloud-based technology. This project entails creating an Azure Virtual Machine (VM) and restoring a database before migrating the database and simulating a disataer recovery for data loss. 

I have included a brief description and analysis of each section before delving into the step-by-step process I took to achieve this project. 

## Production Environment Setup

In a production environment, a database can be established and made available for client presentation. On the other hand, the development environment serves as an isolated space where developers can test and create new features. These are only transferred to the production environment after testing to ensure functionality and prevent accidental data loss.

In the following steps, I will outline the creation of a production environment to restore the Microsoft SQL Server 'AdventureWorks' production database, which is an illustrative and comprehensive sample database provided which emulates a fictional manufacturing company's operations, effecitvely replicating a real-world production database scenario.

I've chosen the Windows 11 Pro operating syster as my virtual machine as this setup simulates a secure and dedicated data storage solution that is likely what an on-premise system within a company would use for this type of database manipluation. Running a Windows 11 VM on my local machine offers several advantages such as being able to access exclusive windows applications and software, and being able to run this software without the need for a seperate physical Windows machine. 

Azure VMs are a type of Infrastructure-as-a-Service (Iaas) which enables the user to create and run virtual machines in the cloud. It is a software emulation of a physical computer system that runs on top of a hypervisor. A hypervisor is a layer of software allowing multiple VMs to run on a single physical server. When creating my machine, to enable incoming network traffic from my port specifically I needed to create an inport bound rule which would allow me to connect through the Remote Desktop Protocol (RDP), as by default, all inbound traffic is blocked for security purposes.

I hosted my database on Azure SQL as it is a cloud-based relational database service providing a managed and scalable platform for running SQL Server databases on the cloud. Azure SQL is built on SQL Server technology, a widely-used relational database manage,ent system (RDBMS). It offers many of the same features and functionality as SQL Server but with the added benefits of cloud computing, as well as enhanced performance, scalability and security. It offers three different types of deployment options (Azure SQL Database, Azure SQL Elastic Pool, and Azure SQL Managed Instance). For this project I used Azure SQL Database as a fully managed, scalabale and highly available database service which offers automatic failover and point-in-time restore capabilities. I needed to install SQL Server onto my VM and SQL Server Management Studio (SSMS). 

"As businesses increasingly embrace cloud computing to drive innovation and scalability, the migration of databases from on-premise environments to the cloud has become a strategic imperative. To achieve a seamless transition, a powerful and user-friendly database management tool is essential. Enter Azure Data Studio, a cutting-edge cross-platform solution developed by Microsoft to simplify database management and streamline the migration process.
Azure Data Studio provides a feature-rich environment, catering to the diverse needs of database administrators, developers, and data professionals. Azure Data Studio empowers users to manage various data platforms, including SQL Server, Azure SQL Database, PostgreSQL, MySQL, and more. With its seamless integration with Azure services, it offers a unified interface to manage both local and cloud-based databases, making it the perfect companion for database migration." - notebook!

__SQL Server__ is a relational database mamagement system (RDBMS) developed by Microsoft to sotre and manage vast quantities of data and supports structured query language (SQL) and Transact-SQL (T-SQL). __SQL Server Management Studio (SSMS)__ is a powerful graphical user interface (GUI) tool for managing SQL Server instances and databases. It is a central hub for database administrators and ddvelopers to perform various tasks related to SQL Server. In this project I have used both of these resources to manage the AdventureWorks database.

To download the database onto SQL server, I used the AdventureWorks database provided by Microsoft. This is a ______ etc. I used SSMS's backup option to get the database onto my machine by downloading the .bak file and then  

The follwing outlines this process in more detail:

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

Azure SQL Database is an online database service that has high availability and disaster recovery capabilities. It provides automated backups to protect data from loss or corruption and minimise downtime, with the option to change how frequently your data is backed up and how long each backup is retained. It's security is also advanced, with it supporting Microsoft Entra ID authentication and role-based access control as well as a built-in firewall restricting access depending on IP addresses or ranges. The encrypted data uses Transparent data encryption (TDE) which protects against unauthorised access. There are two main compute tiers: Serverless Computer tier is chosen as I don't need to pre-allocate any resources and the database automaticall scales resources up or down based on the workload demand, saving unexpected costs as you only pay for the resources I use.

In order to transition a database onto Azure's cloud ecosystem, it needs to be migrated from the on-premise database to an Azure SQL Database, which serves as a target for migrating my on-premise database.

TTo achieve this, the powerful and user-friendly database managemet tool developed by Microsoft `Azure Data Studio` can be used to manage various data platforms including Azure SQL Database, SQL Server PostgreSQL and MySqL, among others. It it a powerful tool which simplifies database management tasks.

These need the appropriate firewalls rules and settings, including IP address. Prior to migration, I needed to prepare for migration by ________ and connecting to Azure SQL Database. 

steps were followed to ensure the database schema and data were transferred successfully using the Azure Data Studio app.

The following steps outline the requirements for installing and configuring Azure Data Studio, connecting to a local SQL Server Database, connecting to an Azure SQL Database, before migrating the schema (using SQL Server Schema Compare extnesion) followed by the data (using the Azure SQL Migration extension).

Firstly, the schema needed to be migrated using ______, which was then followed by the data migration.

To ensure successful migration, the ____ was checked.

"Azure SQL Database is a fully-managed, Platform-as-a-Service (PaaS) offering within Azure, built on the foundation of Azure SQL Server. Azure SQL Server is a cloud-based implementation of Microsoft SQL Server, a widely-used relational database management system (RDBMS). It provides a fully-managed environment for hosting SQL databases in the cloud, eliminating the need for on-premises hardware and maintenance." - collab nb

The SQL Database requires a new server which will act as a container for the database and requires sql server authentication to be set up so one can log in from SQL Server.

Firstly, the local database is connected to, and then the Azure SQL Database that's freshly created. This requires a few different takes to connect, as the firewall rules need to be set n Microsoft Azure portal. After connection, the

"By default, Azure SQL Database is configured to deny all public connections. To connect to your database from outside of Azure, you will need to enable public access by configuring a firewall rule that allows traffic from your client IP address. To do this, first navigate to your Azure SQL Database in the Azure portal. Click on the Set server firewall tab on the top middle of the screen.

Public network access to a SQL Server instance is controlled by the Public Network Access setting. This setting determines whether traffic from the public internet is allowed to reach your SQL Server instance.

By default, the Public Network Access setting is set to Deny, which means that only traffic from within the Azure network is allowed to reach your SQL Server instance. To allow traffic from the public internet to reach your SQL Server instance, you can change the Public Network Access setting to Selected networks."

I then had to connect to the on-premise db from Azure Data Studio. 

The follwing outlines this process in more detail:

[Back to Table of Contents](#table-of-contents)
[Skip to Backing up the On-Premise Database](#Backing-up-the-On-Premise-Database)

### Setting Up Azure SQL Database

1. In the Azure Portal, I navigated to `SQL Database` > `Create`.
2. I created a new resource group 'adventureworks-rg, and named the database 'AdventureWorks2022'.
3. I created a new server with the name 'ad-works-server', geographic location UK South and SQL Server Authentication username and password. I configured compute to the `Basic` storage plan.
4. I clicked `Review + Create` > `Create` before navigating to the resource to add some firewall rules.

<img width="800" alt="Screenshot 2024-02-01 at 14 53 57" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/4aa98ed5-d505-4038-a9d4-09f476e76625">

4. I clicked `Set Server Firewall` > `Selected Network` and pressed `Save`.

<img width="900" alt="Screenshot 2024-02-01 at 15 24 17" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/3fa443e1-814d-4dbc-bf54-0bed792137f0">



6. To connect to this Azure SQL Database, VSCode was used on my local machine.
7. I installed the VSCode `SQL Server` extension and used it to `+ Connection`.
8. This prompts the entry of `Server Name`,`DB Name`, `SQL Login details` and `Display name`, however this prompted me to `Add account` so I signed into Azure using my Account details.
<img width="469" alt="Screenshot 2024-02-01 at 15 33 02" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/e4380905-fc92-4d32-ac19-6d5d5092fc2f">
10. I repeated the VSCode `+ Connection` steps again, and it prometed me to add a firewall rule. I clicked on the promt and it autofilled my local machine's IP address into the 'Start' and 'Finish' points.
<img width="464" alt="Screenshot 2024-02-01 at 15 35 55" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/38f0c136-b514-46c6-a444-1f949095dcbb">
<img width="635" alt="Screenshot 2024-02-01 at 15 38 01" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/b75d2398-35a5-4d4d-8e8c-9228421c1c65">

12. Once again, I pressed `+ Connection` on VSCode and was able to connect successfully to the Azure SQL Database.
13. <img width="258" alt="Screenshot 2024-02-01 at 15 41 35" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/4219ac76-7251-49a5-8243-cf3a866b6c03">


### Preparing for Migration

1. To connect to the on-premise database on my VM, I downloaded the Windows version of `Azure Data Studio`. I installed the programme and clicked `Create a desctop icon` to be able to access the app quickly.
2. I launched `Azure Data Studio` and clicked `New Connection`.
   
<img width="1023" alt="Screenshot 2024-02-01 at 15 43 17" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/6c42c41e-7409-4fb8-a28e-fe190eaab6e8">

4. I used 'localhost' as the server name, with Windows Azure authentication type. I selected AdventureWorks2022 as the database > `Connect` > `Enable Trust Certificate`.


6. To connect to the Azure SQL Database
   
### Schema Migration
### Data Migration 
To esnure this was completed successfully, I used the following input and outputs.

## Data Backup and Restore

Before creating a development environment for this database, I had to ensure the stored data in the production database is secure. The production database is for storing real customer data and the development database is for experimental testing. By provisioning a development database, it ensures there will be no accidental data loss or corruption to the production database. Maintains the integrity of the live data.

-- I backed up the on-premise database by generating a full backup of the production database hosted on the Windows VM, and stored it in the backup file of the VM, before storing it in an Azure's blob. Then, I restored the database in a development environemnt and configured the settings for automatic backups. -- 

-- Data loss or corruption due to unexpected events or cyber threats can have massive negative implications to organisations, meaning backups are an essential aspect of managing databases. Azure SQL Database is used as a 'repository' for these companies who regularly backup and restore their databases using this system to ensure the quick restoration of data if required and minimise downtime. 

"In this example, our Windows VM holds our current production environment and database. We want to perform this backup, so we can later restore the database to a development environment where our developers can work on testing and developing new features. For creating a backup that can be restored to a development environment, we will choose the Full backup type."

Backing up a database is the creation of a copy of the database at a set point in time, and there are full backups (capturing the entire database at a specific point), a differential backup (captures just the new changes made since last full backup) and transaction log backups (). 

Finally, I created an automated backup solution for the development environment, which safeguards any work that is done in future and aneables swift recovery from errors or data loss. There are multiple choices of the times each data backup occurs, with varying cost although it is automatically set to save every 24 hours. 

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

Azure offers a wide range of disaster recovery capabilities designed to protect data, applications and services running on the platform. 

This section of the project focuses on simulating the loss of data in a production environemnt, and being able to recover the lost or corrupt data from the development environment. The purpose of this testing is 

Usually, in a real-life setting, this testing is completed using the development environment as deleting any data which is live can have negative impact if the data isn't being 

This is being complete using the production environment using my initial VM, as it simulated a real-life data loss scenario and the data isn't critical. I noted down the exact data that I corrupted or deleted so I could check these data after recovery. 

In the real world it's improtant to documentation this process, as the exact steps can be followed to reverse any negative outcomes from a real-life incidents and minimize the impact on critical business operations.

Additioanlly, communicating to stakeholders when testing is crucial to build trust and prevent any confusion and be transparent. 

The following segments outline the steps I took:

I used the Azure SQL Database backup to restore the lost data, as the production environment held the full database and fully uploads it to Azure weekly.

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

One feature in Azure SQL Database is Geo-Replication, which asynchronously replicares the database to a secondary region. This is a service that provides data redundancy () and high availability in the case of a regional outage or disaster, and the database can be restored from the second geographical location.

The primary database is the original that serves your application and handles read/write operations, and the seconday database is a read-only copy that is located in a diffrent Azure region. To minimize performacy impact, they are synchronized asychronosly.

This section of the project configures the geo-replication for the production database, increading sata protection by establishing a synchronised copy of the database in a secondary region. This strategic redundancy ensures continous data availability and minimises potential downtime during unforseen disruptions.

I oversee failover tests aimed at simulating real-world scenarios. A planner failover to the secondary region allows access to the secondary database.

Failover is the process of switching the workload from primary to secondary region in a georeplciated environemnt and is normally performed during planned maintenance or in repsponse to a disaster in the primary region. Failover ensures high availability and business continuity by allowing applications to continue running from the seocndayr region. 

Tailback is the process of reverting the workload back to the primary region after successfgul failover. 

Testing failover is crucial to ensure the vaility of the faioover environemnt without impacting the procuction database/workload.

Below are the steps I took to complete this part of the project.


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

7. To test the database works fine in the switched server, I connected to the server through Azure Data Studio on my production vm by copying the endpoint and pasting it into ADS and inputting the credentials I made for the geo replication process. I pressed connect and queried the database to  th

7. As the failover was a success, I repeated the failover to switch the primary server back to UK South.

## Microsoft Entra Directory Integration

To manage who can access the data, I integrated Microsoft Entra Directory with my Azure SQL Database setup. In this section, I 

To ensure only set individuals or groups are able to access this resource, 

### Configuring Microsoft Entra ID for Azure SQL Database

### Creating a Database Reader User

## Licence Information





