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
6. [Data Backup and Restore](#data-backup-and-restore)
7. [Disaster Recovery Simulation](#disaster-recovery-simulation)
8. [Geo Repliacation and Failover](#geo-replication-and-failover)
9. [Microsoft Entra Directory Integration](#microsoft-entra-directory-integration)
10. [Licence Information](#licence-information)

## 1. Introduction 

Welcome to my Multinational Retail Data Centralisation Project! 

This README.md outlines the process of setting up a cloud-based database system on Microsoft Azure, showcasing my skills in this cloud-based technology. This project entails creating an Azure Virtual Machine (VM) and restoring a database before migrating the database and simulating a disataer recovery for data loss. [fill in more later]

__Why MS Azure? List pros. touch on cons?__

## 2. Production Environment Setup

__general info about this stage__

In a production environment, a database can be established and made available for client presentation. On the other hand, the development environment serves as an isolated space where developers can test and create new features. These are only transferred to the production environment after testing to ensure functionality and prevent accidental data loss.

In the following steps, I will outline the creation of a production environment to restore the Microsoft SQL Server 'AdventureWorks' production database, which is an illustrative and comprehensive sample database provided which emulates a fictional manufacturing company's operations, effecitvely replicating a real-world production database scenario. 

I've chosen the Windows 11 Pro as this setup simulates a secure and dedicated data storage solution that is likely what an on-premise system within a company would use for this type of database manipluation. Running a Windows 11 VM on my local machine offers several advantages such as being able to access exclusive windows applications and software, and being able to run this software without the need for a seperate physical Windows machine. 

Azure VMs are a type of Infrastructure-as-a-Service (Iaas) which enables the user to create and run virtual machines in the cloud. It is a software emulation of a physical computer sustem that runs on ntop of a hypervisor. A hypervisor is a layer of software allowing multiple VMs to run on a single physical server. 


### 2.1. Provisioning the Windows Virtual Machine

__step-wise steps__

1. A Microsoft Azure Account was created from the local machine's browser.
2. Within the Microsoft Azure Portal's home page, I navigated to the `Virtual Machines` page > `+ Create` > `Azure Virtual Machine` to start the process.
4. The following page required the addition of several key pieces of information:
     1. `Name`: _____Create a name for the virtual machine______. This is normal.
5. This 

In order to set up a Windows Virtual Machine (VM), 

To enable the connection

Ensure that you configure the appropriate network settings and firewall rules. This configuration will enable you to establish a connection to the VM using the RDP protocol.

In order to 

Provide insights into the virtual machine setup, SQL Server installation, and the creation of the production database.

### 2.2. SQL Server and SSMS

### 2.3. Creating the Production Database

What the database is - describe etc.
What is a production database.

### 3. Azure SQL Database Migration 

Azure SQL Databse is an online databse service that has high availability and disaster recovery capabilities. It provides automated backups to protect data from loss or corruption and minimise downtime, with the option to change how frequently your data is backed up and how long each backup is retained. It's security is also advanced, with it supporting Microsoft Entra ID authentication and role-based access control as well as a built-in firewall restricting access depending on IP addresses or ranges. The encrypted data uses Transparent data encryption (TDE) which protects against unauthorised access. There are two main compute tiers: Serverless Computer tier is chosen as I don't need to pre-allocate any resources and the database automaticall scales resources up or down based on the workload demand, saving unexpected costs as you only pay for the resources I use.

In order to transition a database onto Azure's cloud ecosystem, it needs to be migrated from the on-premise database to an Azure SQL Database, which serves as a target for migrating my on-premise database.

TTo achieve this, the powerful and user-friendly database managemet tool developed by Microsoft `Azure Data Studio` can be used to manage various data platforms including Azure SQL Database, SQL Server PostgreSQL and MySqL, among others. It it a powerful tool which simplifies database management tasks.

These need the appropriate firewalls rules and settings, including IP address. Prior to migration, I needed to prepare for migration by ________ and connecting to Azure SQL Database. 

steps were followed to ensure the database schema and data were transferred successfully using the Azure Data Studio app.

The following steps outline the requirements for installing and configuring Azure Data Studio, connecting to a local SQL Server Database, connecting to an Azure SQL Database, before migrating the schema (using SQL Server Schema Compare extnesion) followed by the data (using the Azure SQL Migration extension).

Firstly, the schema needed to be migrated using ______, which was then followed by the data migration.

To ensure successful migration, the ____ was checked.

"Azure SQL Database is a fully-managed, Platform-as-a-Service (PaaS) offering within Azure, built on the foundation of Azure SQL Server. Azure SQL Server is a cloud-based implementation of Microsoft SQL Server, a widely-used relational database management system (RDBMS). It provides a fully-managed environment for hosting SQL databases in the cloud, eliminating the need for on-premises hardware and maintenance." - collab nb

The follwing outlines this process in more detail:

## 3.1. Setting Up Azure SQL Database

1. In the Azure Portal online, create a resource >
2. Create server
3. set firewall rules

<img width="800" alt="Screenshot 2024-02-01 at 14 53 57" src="https://github.com/Claudiomics/azure-database-migration/assets/149532217/4aa98ed5-d505-4038-a9d4-09f476e76625">

"By default, Azure SQL Database is configured to deny all public connections. To connect to your database from outside of Azure, you will need to enable public access by configuring a firewall rule that allows traffic from your client IP address. To do this, first navigate to your Azure SQL Database in the Azure portal. Click on the Set server firewall tab on the top middle of the screen.

Public network access to a SQL Server instance is controlled by the Public Network Access setting. This setting determines whether traffic from the public internet is allowed to reach your SQL Server instance."

##

1. On your VM, navigate to Azure Data Studio and download the Windows version of `Azure Data Studio`. Install the programme and click `Crete a desctop icon` to be able to access this app quickly.
2. Launch `Azure Data Studio` and 
