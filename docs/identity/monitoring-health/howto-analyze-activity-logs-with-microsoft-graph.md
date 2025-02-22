---
title: How to analyze activity logs with Microsoft Graph
description: Learn how to analyze sign-in and audit logs with Microsoft Graph.
author: shlipsey3
manager: amycolannino
ms.service: entra-id
ms.topic: how-to
ms.subservice: monitoring-health
ms.date: 02/07/2024
ms.author: sarahlipsey
ms.reviewer: egreenberg

# Customer intent: As an IT admin, I want to be able to view and analyze sign-in and audit logs with the Microsoft Graph API so I can monitor and troubleshoot user activity in my organization programmatically.

---
# How to analyze activity logs with Microsoft

The Microsoft Entra [reporting APIs](/graph/api/resources/azure-ad-auditlog-overview) provide you with programmatic access to the data through a set of REST APIs. You can call these APIs from many programming languages and tools. The Microsoft Graph API isn't designed for pulling large amounts of activity data. Pulling large amounts of activity data using the API might lead to issues with pagination and performance.

This article describes how to analyze Microsoft Entra activity logs with Microsoft Graph Explorer and Microsoft Graph PowerShell.

## Prerequisites

- For license and role requirements, see [Microsoft Entra monitoring and health licensing](../../fundamentals/licensing.md#microsoft-entra-monitoring-and-health).
- [Global Administrator](~/identity/role-based-access-control/permissions-reference.md#global-administrator) role to consent to required permissions.
- 

To make requests to the Microsoft Graph API, you must first:

- [Register your app](/graph/auth-register-app-v2)
- [Get authentication tokens for a user](/graph/auth-v2-user) or [service](/graph/auth-v2-service)

## Access reports using Microsoft Graph Explorer

With all the prerequisites configured, you can run activity log queries in Microsoft Graph. For more information on Microsoft Graph queries for activity logs, see [Activity reports API overview](/graph/api/resources/azure-ad-auditlog-overview).

1. Start [Microsoft Graph Explorer tool](https://aka.ms/ge).

1. Select your profile and then select **Modify permissions**.

1. Consent to the following required permissions:
    - `AuditLog.Read.All`
    - `Directory.Read.All`

1. Use one of the following queries to start using Microsoft Graph for accessing activity logs:
    - GET `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits`
    - GET `https://graph.microsoft.com/v1.0/auditLogs/signIns`
    - GET `https://graph.microsoft.com/v1.0/auditLogs/provisioning`

    ![Screenshot of an activity log GET query in Microsoft Graph.](media/howto-configure-prerequisites-for-reporting-api/graph-sample-get-query.png)

### Fine-tune your queries

To search for specific activity log entries, use the $filter query parameter with one of the available properties. 

- [Sign-in log properties](/graph/api/resources/signin#properties)
- [Audit log properties](/graph/api/resources/directoryaudit#properties)

Try using the following queries:

- For sign-in attempts where Conditional Access failed:
  - GET `https://graph.microsoft.com/v1.0/auditLogs/signIns$filter=conditionalAccessStatus eq 'failure'`

- To find sign-ins to a specific application:
  - GET `https://graph.microsoft.com/v1.0/auditLogs/signIns?filter=(createdDateTime ge 2024-01-13T14:13:32Z and createdDateTime le 2021-01-14T17:43:26Z) and appId eq 'APP ID'`

### Related APIs

Once you're familiar with the standard sign-in and audit logs, try exploring these other APIs:

- [Identity Protection APIs](/graph/api/resources/identityprotection-overview)
- [Provisioning logs API](/graph/api/resources/provisioningobjectsummary)

## Access reports using Microsoft Graph PowerShell

You can use PowerShell to access the Microsoft Entra reporting API. For more information, see [Microsoft Graph PowerShell overview](/powershell/microsoftgraph/overview). 

Microsoft Graph PowerShell cmdlets:

- **Audit logs:** `Get-MgAuditLogDirectoryAudit`
- **Sign-in logs:** `Get-MgAuditLogSignIn`
- **Provisioning logs:** `Get-MgAuditLogProvisioning`
- Explore the full list of [reporting-related Microsoft Graph PowerShell cmdlets](/powershell/module/microsoft.graph.reports/).


## Common errors
<a name='troubleshoot-errors-in-azure-active-directory-reporting-api'></a>

**Error: Neither tenant is B2C or tenant doesn't have premium license**: Accessing sign-in reports requires a Microsoft Entra ID P1 or P2 license. If you see this error message while accessing sign-ins, make sure that your tenant is licensed with a Microsoft Entra ID P1 license.

**Error: User isn't in the allowed roles**: If you see this error message while trying to access audit logs or sign-ins using the API, make sure that your account is part of the **Security Reader** or **Reports Reader** role in your Microsoft Entra tenant.

**Error: Application missing Microsoft Entra ID 'Read directory data' or 'Read all audit log data' permission**: The application must have either the `AuditLog.Read.All` or `Directory.Read.All` permission to access the activity logs with Microsoft Graph.

## Next steps

- [Get started with Microsoft Entra ID Protection and Microsoft Graph](../../id-protection/howto-identity-protection-graph-api.md)
- [Audit API reference](/graph/api/resources/directoryaudit)
- [API signIn reference](/graph/api/resources/signin)