---
title: "Modify Statistics"
description: Learn how to modify existing statistics in SQL Server by using SQL Server Management Studio or Transact-SQL.
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.date: "03/14/2017"
ms.service: sql
ms.subservice: performance
ms.topic: conceptual
ms.custom:
  - ignite-2024
helpviewer_keywords:
  - "statistics [SQL Server], modifying"
  - "modifying statistics"
monikerRange: ">=aps-pdw-2016 || =azuresqldb-current || =azure-sqldw-latest || >=sql-server-2016 || >=sql-server-linux-2017 || =azuresqldb-mi-current || =fabric"
---
# Modify Statistics
[!INCLUDE [SQL Server Azure SQL Database Synapse Analytics PDW FabricSQLDB](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw-fabricsqldb.md)]
  You can modify existing statistics in [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] by using [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] or [!INCLUDE[tsql](../../includes/tsql-md.md)].  
  
 **In This Topic**  
  
-   **Before you begin:**  
  
     [Security](#Security)  
  
-   **To modify statistics, using:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="BeforeYouBegin"></a> Before You Begin  
  
###  <a name="Security"></a> Security  
  
####  <a name="Permissions"></a> Permissions  
 Requires that:  
  
-   The user has ALTER permission on the table or view.  
  
-   The user be the table or indexed view owner, or a member of one of the following roles: **sysadmin** fixed server role, **db_owner** fixed database role, or the **db_ddladmin** fixed database role.  
  
##  <a name="SSMSProcedure"></a> Using SQL Server Management Studio  
  
#### To modify statistics  
  
1.  In **Object Explorer**, click the plus sign to expand the database in which you want to modify a statistic.  
  
2.  Click the plus sign to expand the **Tables** folder.  
  
3.  Click the plus sign to expand the table in which you want to modify a statistic.  
  
4.  Click the plus sign to expand the **Statistics** folder.  
  
5.  Right-click the statistics object that you wish to modify and select **Properties**.  
  
6.  In the **Statistics Properties -** *statistics_name* dialog box, on the **General** page, click **Add**, **Remove**, **Move Up**, or **Move Down**, or any combination, to alter the properties of the statistics. Remember that a column's location within the **Statistics Columns** grid can substantially impact the usefulness of the statistics.  
  
7.  Click **OK**.  

##  <a name="TsqlProcedure"></a> Using Transact-SQL  
 **To modify statistics**  
  
 This task cannot be performed using Transact-SQL statements. To modify statistics using Transact-SQL, you must first delete the existing statistic and then re-create it with new attributes.  
  
 For more information, see [DROP STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/drop-statistics-transact-sql.md) and [CREATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/create-statistics-transact-sql.md).  
  
  
