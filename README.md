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
5. [Data Backup and Restore](#data-backup-and-restore)
6. [Disaster Recovery Simulation](#disaster-recovery-simulation)
7. [Geo Repliacation and Failover](#geo-replication-and-failover)
8. [Microsoft Entra Directory Integration](#microsoft-entra-directory-integration)
9. [Licence Information](#licence-information)

## Introduction 

Welcome to my Multinational Retail Data Centralisation Project! 

This README.md outlines the process of setting up a cloud-based database system on Microsoft Azure, showcasing my skills in this cloud-based technology. This project entails creating an Azure Virtual Machine (VM) and restoring a database before migrating the database and simulating a disataer recovery for data loss. [fill in more later]

__Why MS Azure? List pros. touch on cons?__

## Production Environment Setup

In a production environment, a database can be established and made available for client presentation. On the other hand, the development environment serves as an isolated space where developers can test and create new features. These are only transferred to the production environment after testing to ensure functionality and prevent accidental data loss.

In the following steps, I will outline the creation of a production environment to restore the Microsoft SQL Server 'AdventureWorks' production database, which is an illustrative and comprehensive sample database provided which emulates a fictional manufacturing company's operations, effecitvely replicating a real-world production database scenario. 

### Provisioning the Windows Virtual Machine

I've chosen the Windows 11 Pro as this setup simulates a secure and dedicated data storage solution that is likely what an on-premise system within a company would use for this type of database manipluation. Running a Windows 11 VM on my local machine offers several advantages such as being able to access exclusive windows applications and software, and being able to run this software without the need for a seperate physical Windows machine. 
Being able to run a virtual machine on my laptop is incredibly helpful. __more info__ 

Here are the steps I followed to create and connect to my VM from my local MacBook Pro:
`Is this showing as a tag?`
In order to set up a Windows Virtual Machine (VM), 

To enable the connection

Ensure that you configure the appropriate network settings and firewall rules. This configuration will enable you to establish a connection to the VM using the RDP protocol.

In order to 

Provide insights into the virtual machine setup, SQL Server installation, and the creation of the production database.

### SQL Server and SSMS

### Creating the Production Database

What the database is - describe etc.
What is a production database.

### Azure SQL Database Migration

Describe database migration:

##

