# Azure Database Migration Project

![Microsoft](https://img.shields.io/badge/Microsoft-0078D4?style=for-the-badge&logo=microsoft&logoColor=white)
![Azure](https://img.shields.io/badge/azure-%230072C6.svg?style=for-the-badge&logo=microsoftazure&logoColor=white)
![MicrosoftSQLServer](https://img.shields.io/badge/Microsoft%20SQL%20Server-CC2927?style=for-the-badge&logo=microsoft%20sql%20server&logoColor=white)

# Table of Contents
1. [1. Introduction](#1.-introduction)
2. [Production Environment Setup](#production-environment-setup)
   1. [Provisioning the Windows Virtual Machine](#provisioning-the-windows-virtual-machine)
   2. [SQL Server and SSMS](#sql-server-and-ssms)
   3. [Creating the Production Database](#creating-the-production-database)
4. [Azure SQL Database Migration](#azure-sql-database-migration)
   1. [Setting Up Azure SQL Database](setting-up-azure-sql-database)
   2. [Preparing for Migration](#preparing-for-migration)
   3. [Schema Migration](#schema-migration)
   4. [Data Migration](#data-migration)
6. [Data Backup and Restore](#data-backup-and-restore)
   1. [Backing up the On-Premise Database](#backing-up-the-on-premise-database)
   2. [Uploading to Blob Storage](#uploading-to-blob-storage)
   3. [Restoring the Database on Development Environment](#restoring-the-database-on-development-environment)
   4. [Automating Backups for Development Database](automating-backups-for-development-database)
8. [Disaster Recovery Simulation](#disaster-recovery-simulation)
   1. [Mimicing Data Loss in Production Environment](#mimicing-data-loss-in-production-environment)
   2. [Restoring Database from Azure SQL Database Backup](#restoring-database-from-azure-sql-database-backup)
10. [Geo Repliacation and Failover](#geo-replication-and-failover)
   1. [Setting up Geo-Replication for Azure SQL Database](#setting-up-geo-replication-for-azure-sql-database)
   2. [Testing Failover and Failback](#testing-failover-and-failback)
12. [Microsoft Entra Directory Integration](#microsoft-entra-directory-integration)
   1. [Configuring Microsoft Entra ID for Azure SQL Database](#configuring-microsoft-entra-id-for-azure-sql-database)
   2. [Creating a Database Reader User](#creating-a-database-reader-user)
14. [Licence Information](#licence-information)

## 1. Introduction 

Welcome to my Multinational Retail Data Centralisation Project!


This README.md outlines the process of setting up a cloud-based database system on Microsoft Azure, showcasing my skills in this cloud-based technology. This project entails creating an Azure Virtual Machine (VM) and restoring a database before migrating the database and simulating a disataer recovery for data loss. 

I have included a brief description and analysis of each section before delving into the step-by-step process I took to achieve this project. 

## 2. Production Environment Setup

In a production environment, a database can be established and made available for client presentation. On the other hand, the development environment serves as an isolated space where developers can test and create new features. These are only transferred to the production environment after testing to ensure functionality and prevent accidental data loss.

In the following steps, I will outline the creation of a production environment to restore the Microsoft SQL Server 'AdventureWorks' production database, which is an illustrative and comprehensive sample database provided which emulates a fictional manufacturing company's operations, effecitvely replicating a real-world production database scenario.

I've chosen the Windows 11 Pro operating syster as my virtual machine as this setup simulates a secure and dedicated data storage solution that is likely what an on-premise system within a company would use for this type of database manipluation. Running a Windows 11 VM on my local machine offers several advantages such as being able to access exclusive windows applications and software, and being able to run this software without the need for a seperate physical Windows machine. 

Azure VMs are a type of Infrastructure-as-a-Service (Iaas) which enables the user to create and run virtual machines in the cloud. It is a software emulation of a physical computer system that runs on top of a hypervisor. A hypervisor is a layer of software allowing multiple VMs to run on a single physical server. When creating my machine, to enable incoming network traffic from my port specifically I needed to create an inport bound rule which would allow me to connect through the Remote Desktop Protocol (RDP), as by default, all inbound traffic is blocked for security purposes.

I hosted my database on Azure SQL as it is a cloud-based relational database service providing a managed and scalable platform for running SQL Server databases on the cloud. Azure SQL is built on SQL Server technology, a widely-used relational database manage,ent system (RDBMS). It offers many of the same features and functionality as SQL Server but with the added benefits of cloud computing, as well as enhanced performance, scalability and security. It offers three different types of deployment options (Azure SQL Database, Azure SQL Elastic Pool, and Azure SQL Managed Instance). For this project I used Azure SQL Database as a fully managed, scalabale and highly available database service which offers automatic failover and point-in-time restore capabilities. I needed to install SQL Server onto my VM and SQL Server Management Studio (SSMS). 

"As businesses increasingly embrace cloud computing to drive innovation and scalability, the migration of databases from on-premise environments to the cloud has become a strategic imperative. To achieve a seamless transition, a powerful and user-friendly database management tool is essential. Enter Azure Data Studio, a cutting-edge cross-platform solution developed by Microsoft to simplify database management and streamline the migration process.
Azure Data Studio provides a feature-rich environment, catering to the diverse needs of database administrators, developers, and data professionals. Azure Data Studio empowers users to manage various data platforms, including SQL Server, Azure SQL Database, PostgreSQL, MySQL, and more. With its seamless integration with Azure services, it offers a unified interface to manage both local and cloud-based databases, making it the perfect companion for database migration." - notebook!

__SQL Server__ is a relational database mamagement system (RDBMS) developed by Microsoft to sotre and manage vast quantities of data and supports structured query language (SQL) and Transact-SQL (T-SQL). __SQL Server Management Studio (SSMS)__ is a powerful graphical user interface (GUI) tool for managing SQL Server instances and databases. It is a central hub for database administrators and ddvelopers to perform various tasks related to SQL Server. In this project I have used both of these resources to manage the AdventureWorks database.

To download the database onto SQL server, I used the AdventureWorks database provided by Microsoft. This is a ______ etc. I used SSMS's backup option to get the database onto my machine by downloading the .bak file and then  

### 2.1. Provisioning the Windows Virtual Machine

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

### 2.2. SQL Server and SSMS

1. I downloaded `SQL Server Installer` from the Microsoft Download Centre on my VM's browser.
2. I ran the SQL Server `Installation Wizard` and chose the `Basic` option.
3. I accpeted the license terms and chose where to install it to on my VM.
4. After it was finished installing, it prompted me to download SQL Server Management Studio (SSMS).
5. After following the installation process, I connected to the SQL Server using SSMS by using 'localhost' as the server name (as the database is on the same machine).
7. I used `Windows Authentication` and my VM's Windows credentials to log in > `Connect`. I could then see my SQL Server instance displayed under the Object Explorer:

<img width="900" alt="Screenshot 2024-01-28 at 22 22 59" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/38d58d8e-1d78-4f9b-885b-0465c8e3e83d">

### 2.3. Creating the Production Database

1.	Firstly, I downloaded the Production Database AdventureWorks from the file provided by Mcirosoft onto my VM. (link https://aicore-portal-public-prod-307050600709.s3.eu-west-1.amazonaws.com/project-files/93dd5a0c-212d-48eb-ad51-df521a9b4e9c/AdventureWorks2022.bak )
2.	I copied the file and put it into ` C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup` on the VM.
3.	With the SQL Server connection active on SSMS, I right-clicked on `Databases` in Object Explorer and chose `Restore Database`.
4.	For the source, I choose `Device`, `…` and `Add` to select the .bak backup file that’s in the Backup folder. 
5.	On the General page, I made sure the database was correct and selected `OK`, which came up with a `Database restored successfully` message.
   <img width="900" alt="Screenshot 2024-01-28 at 22 29 24" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/8f6cfa4f-16ef-4850-aa8b-d61fae999414">

7.	I right clicked the database and made a quick query to make sure everything was working alright.

<img width="900" alt="Screenshot 2024-01-28 at 22 31 21" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/7271b79f-078c-4661-9eca-27dbdb3449e0">

## 3. Azure SQL Database Migration 

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

### 3.1. Setting Up Azure SQL Database

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


### 3.2. Preparing for Migration

1. To connect to the on-premise database on my VM, I downloaded the Windows version of `Azure Data Studio`. I installed the programme and clicked `Create a desctop icon` to be able to access the app quickly.
2. I launched `Azure Data Studio` and clicked `New Connection`.
   
<img width="1023" alt="Screenshot 2024-02-01 at 15 43 17" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/6c42c41e-7409-4fb8-a28e-fe190eaab6e8">

4. I used 'localhost' as the server name, with Windows Azure authentication type. I selected AdventureWorks2022 as the database > `Connect` > `Enable Trust Certificate`.



6. To connect to the Azure SQL Database
### 3.3. Schema Migration
### 3.4. Data Migration 
## 4. Data Backup and Restore

Before creating a development environment for this database, I had to ensure the stored data in the production database is secure. The production database is for storing real customer data and the development database is for experimental testing. By provisioning a development database, it ensures there will be no accidental data loss or corruption to the production database. Maintains the integrity of the live data.

I backed up the on-premise database by generating a full backup of the production database hosted on the Windows VM, and stored it in the backup file of the VM, before storing it in Azure's blob storage solution. T

Finally, I created an automated backup solution for the development environment, which safeguards any work that is done in future and aneables swift recovery from errors or data loss. There are multiple choices of the times each data backup occurs, with varying cost although it is automatically set to save every 24 hours. 

The following steps were taken to achieve this:

### 4.1. Backing up the On-Premise Database

1. 

### 4.2. Uploading to Blob Storage

### 4.3. Restoring the Database on Development Environment

### 4.4. Automating Backups for Development Database

## 5. Disaster Recovery Simulation

### 5.1. Mimicing Data Loss in Production Environment

### 5.2. Restoring Database from Azure SQL Database Backup

## 6. Geo Repliacation and Failover

### 6.1. Setting up Geo-Replication for Azure SQL Database

### 6.2. Testing Failover and Failback

## 7. Microsoft Entra Directory Integration

To manage who can access the data, I integrated Microsoft Entra Directory with my Azure SQL Database setup. In this section, I 

### 7.1. Configuring Microsoft Entra ID for Azure SQL Database

### 7.2. Creating a Database Reader User

## Licence Information





