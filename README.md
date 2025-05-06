# Securing a Microsoft Fabric Data Warehouse 

## Overview
This lab demonstrates how to implement comprehensive security measures in a Microsoft Fabric data warehouse, including dynamic data masking, row-level security, column-level security, and granular SQL permissions.

## Prerequisites
- Microsoft Fabric trial account
  
- Two user accounts (one with Workspace Admin role, one with Workspace Viewer role)
  
- Web browser access

## Lab Steps

### 1. Create Workspace and Data Warehouse
1. Navigate to [Microsoft Fabric home page](https://app.fabric.microsoft.com)
   
2. Create new workspace with admin privileges
   
3. Create new data warehouse in the workspace

### 2. Implement Dynamic Data Masking

```sql

-- Create table with masked columns

CREATE TABLE dbo.Customers

(
   
    CustomerID INT NOT NULL,
  
    FirstName varchar(50) MASKED WITH (FUNCTION = 'partial(1,"XXXXXXX",0)') NULL,
     
    LastName varchar(50) NOT NULL,
     
    Phone varchar(20) MASKED WITH (FUNCTION = 'default()') NULL,
     
    Email varchar(50) MASKED WITH (FUNCTION = 'email()') NULL
   
);

-- Test masking with different user permissions

GRANT UNMASK ON dbo.Customers TO [username@domain.com];

```

### 3. Configure Row-Level Security

```sql

-- Create sales table with user-specific data

CREATE TABLE dbo.Sales (OrderID INT, SalesRep VARCHAR(60), Product VARCHAR(10), Quantity INT);

-- Create security predicate function

CREATE FUNCTION rls.fn_securitypredicate(@SalesRep AS VARCHAR(60))

    RETURNS TABLE WITH SCHEMABINDING

AS RETURN SELECT 1 WHERE @SalesRep = USER_NAME();

-- Apply security policy

CREATE SECURITY POLICY SalesFilter

ADD FILTER PREDICATE rls.fn_securitypredicate(SalesRep) ON dbo.Sales;
```

### 4. Implement Column-Level Security

```sql
-- Create orders table

CREATE TABLE dbo.Orders (OrderID INT, CustomerID INT, CreditCard VARCHAR(20));

-- Restrict column access

DENY SELECT ON dbo.Orders (CreditCard) TO [username@domain.com];
```

### 5. Configure Granular SQL Permissions

```sql

-- Create test objects

CREATE PROCEDURE dbo.sp_PrintMessage AS PRINT 'Hello World';

CREATE TABLE dbo.Parts (PartID INT, PartName VARCHAR(25));

-- Apply granular permissions

DENY SELECT on dbo.Parts to [username@domain.com];

GRANT EXECUTE on dbo.sp_PrintMessage to [username@domain.com];
```

## Key Learnings

- Implementing multiple layers of data security in Fabric
  
- Restricting data access at row and column levels
  
- Managing user permissions with T-SQL commands
  
- Testing security configurations with different user roles

## Clean Up

1. Navigate to workspace settings
   
3. Select "Remove this workspace"

