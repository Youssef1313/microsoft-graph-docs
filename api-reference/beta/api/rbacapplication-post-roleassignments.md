---
title: "Create unifiedRoleAssignment"
description: "Use this API to create a new unified role assignment."
localization_priority: Normal
author: "davidmu1"
ms.prod: "microsoft-identity-platform"
doc_type: "apiPageType"
---

# Create unifiedRoleAssignment

[!INCLUDE [beta-disclaimer](../../includes/beta-disclaimer.md)]

Use this API to create a new unified role assignment.

## Permissions

One of the following permissions is required to call this API. To learn more, including how to choose permissions, see [Permissions](/graph/permissions-reference).

| Permission type                        | Permissions (from least to most privileged) |
|:---------------------------------------|:--------------------------------------------|
| Delegated (work or school account)     | RoleManagement.Read.Directory, RoleManagement.ReadWrite.Directory |
| Delegated (personal Microsoft account) | Not supported. |
| Application                            | RoleManagement.Read.Directory, RoleManagement.ReadWrite.Directory |

## HTTP request

<!-- { "blockType": "ignored" } -->

```http
POST /roleManagement/directory/roleAssignments
```

## Request headers

| Name          | Description   |
|:--------------|:--------------|
| Authorization | Bearer {token} |

## Request body

In the request body, supply a JSON representation of [unifiedRoleAssignment](../resources/unifiedroleassignment.md) object.

## Response

If successful, this method returns `201, Created` response code and a new [unifiedRoleAssignment](../resources/unifiedroleassignment.md) object in the response body.

## Examples

### Request

The following is an example of the request. Note the use of the roleTemplateId for roleDefinitionId. roleDefinitionId can be either the service-wide template Id or the directory-specific roleDefinitionId.
<!-- {
  "blockType": "request",
  "name": "create_unifiedroleassignment_from_rbacapplication"
}-->

```http
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json

{ 
    "principalId":"a98eb769-7bd4-4489-86f6-ad96e1d58b62",
    "roleDefinitionId":"b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "resourceScope":"/"
}
```

### Response

The following is an example of the response.

> **Note:** The response object shown here might be shortened for readability. All the properties will be returned from an actual call.

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.unifiedRoleAssignment"
} -->

```http
HTTP/1.1 201 Created
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignments/$entity",
    "id": "YUb1sHQtUEyvox7IA_Eu_mm3jqnUe4lEhvatluHVi2I-1",
    "principalId": "a98eb769-7bd4-4489-86f6-ad96e1d58b62",
    "resourceScope": "/",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe"
}
```

<!-- uuid: 16cd6b66-4b1a-43a1-adaf-3a886856ed98
2019-02-04 14:57:30 UTC -->
<!-- {
  "type": "#page.annotation",
  "description": "Create unifiedRoleAssignment",
  "keywords": "",
  "section": "documentation",
  "tocPath": ""
}-->