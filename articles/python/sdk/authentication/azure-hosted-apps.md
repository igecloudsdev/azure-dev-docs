---
title: Authenticating Azure-hosted apps to Azure resources with the Azure SDK for Python
description: This article covers how to configure authentication for apps to Azure services when the app is hosted in an Azure service like Azure App Service, Azure Functions, or Azure Virtual Machines.
ms.date: 10/15/2024
ms.topic: how-to
ms.custom: devx-track-python, devx-track-azurecli
---

# Authenticating Azure-hosted apps to Azure resources with the Azure SDK for Python

When you host an app in Azure using services like Azure App Service, Azure Virtual Machines, or Azure Container Instances, the recommended approach to authenticate an app to Azure resources is with [managed identity](/azure/active-directory/managed-identities-azure-resources/overview).

A managed identity provides an identity for your app such that it can connect to other Azure resources without the need to use a secret key or other application secret. Internally, Azure knows the identity of your app and what resources it's allowed to connect to. Azure uses this information to automatically obtain Microsoft Entra tokens for the app to allow it to connect to other Azure resources, all without you having to manage any application secrets.

> [!NOTE]
> Apps running on Azure Kubernetes Service (AKS) can use a workload identity to authenticate with Azure resources. In AKS, a workload identity represents a trust relationship between a managed identity and a Kubernetes service account. If an application deployed to AKS is configured with a Kubernetes service account in such a relationship, `DefaultAzureCredential` authenticates the app to Azure by using the managed identity. Authentication by using a workload identity is discussed in [Use Microsoft Entra Workload ID with Azure Kubernetes Service](/azure/aks/workload-identity-overview?tabs=python). For steps on how to configure workload identity, see [Deploy and configure workload identity on an Azure Kubernetes Service (AKS) cluster](/azure/aks/workload-identity-deploy-cluster).

## Managed identity types

There are two types of managed identities:

- **System-assigned managed identities** - This type of managed identity is provided by and tied directly to an Azure resource. When you enable managed identity on an Azure resource, you get a system-assigned managed identity for that resource. A system-assigned managed identity is tied to the lifecycle of the Azure resource it's associated with. When the resource is deleted, Azure automatically deletes the identity for you. Since all you have to do is enable managed identity for the Azure resource hosting your code, this approach is the easiest type of managed identity to use.
- **User-assigned managed identities** - You can also create a managed identity as a standalone Azure resource. This approach is most frequently used when your solution has multiple workloads that run on multiple Azure resources that all need to share the same identity and same permissions. For example, if your solution had components that run on multiple App Service and virtual machine instances that all need access to the same set of Azure resources, then a user-assigned managed identity used across those resources makes sense.

This article covers the steps to enable and use a system-assigned managed identity for an app. If you need to use a user-assigned managed identity, see the article [Manage user-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities) to see how to create a user-assigned managed identity.

## 1 - Enable managed identity in the Azure resource hosting the app

The first step is to enable managed identity on Azure resource hosting your app. For example, if you're hosting a Django application using Azure App Service, you need to enable managed identity for the App Service web app that is hosting your app. If you're using a virtual machine to host your app, you would enable your VM to use managed identity.

You can enable managed identity to be used for an Azure resource using either the Azure portal or the Azure CLI.

### [Azure CLI](#tab/azure-cli)

Azure CLI commands can be run in the [Azure Cloud Shell](https://shell.azure.com) or on a workstation with the [Azure CLI installed](/cli/azure/install-azure-cli).

The Azure CLI commands used to enable managed identity for an Azure resource are of the form `az <command-group> identity --resource-group <resource-group-name> --name <resource-name>`. Specific commands for popular Azure services are shown below.

[!INCLUDE [Enable managed identity Azure CLI](<../../../includes/sdk-auth-passwordless/enable-managed-identity-azure-cli.md>)]

The output will look like the following.

```json
{
  "principalId": "aaaaaaaa-bbbb-cccc-1111-222222222222",
  "tenantId": "aaaabbbb-0000-cccc-1111-dddd2222eeee",
  "type": "SystemAssigned",
  "userAssignedIdentities": null
}

```

The `principalId` value is the unique ID of the managed identity. Keep a copy of this output as you'll need these values in the next step.

### [Azure portal](#tab/azure-portal)

| Instructions    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Enable managed identity step 1](<../../../includes/sdk-auth-passwordless/enable-managed-identity-azure-portal-1.md>)] | :::image type="content" source="../../../includes/media/sdk-auth-passwordless/enable-managed-identity-azure-portal-1-240px.png" alt-text="A screenshot showing how to use the top search bar in the Azure portal to locate and navigate to a resource in Azure." lightbox="../../../includes/media/sdk-auth-passwordless/enable-managed-identity-azure-portal-1.png"::: |
| [!INCLUDE [Enable managed identity step 2](<../../../includes/sdk-auth-passwordless/enable-managed-identity-azure-portal-2.md>)] | :::image type="content" source="../../../includes/media/sdk-auth-passwordless/enable-managed-identity-azure-portal-2-240px.png" alt-text="A screenshot showing the location of the Identity menu item in the left-hand menu for an Azure resource." lightbox="../../../includes/media/sdk-auth-passwordless/enable-managed-identity-azure-portal-2.png"::: |
| [!INCLUDE [Enable managed identity step 3](<../../../includes/sdk-auth-passwordless/enable-managed-identity-azure-portal-3.md>)] | :::image type="content" source="../../../includes/media/sdk-auth-passwordless/enable-managed-identity-azure-portal-3-240px.png" alt-text="A screenshot showing how to enable managed identity for an Azure resource on the resource's Identity page." lightbox="../../../includes/media/sdk-auth-passwordless/enable-managed-identity-azure-portal-3.png"::: |

---

## 2 - Assign roles to the managed identity

Next, you need to determine what roles (permissions) your app needs and assign the managed identity to those roles in Azure. A managed identity can be assigned roles at a resource, resource group, or subscription scope. This example shows how to assign roles at the resource group scope since most applications group all their Azure resources into a single resource group.

### [Azure CLI](#tab/azure-cli)

A managed identity is assigned a role in Azure using the [az role assignment create](/cli/azure/role/assignment) command. For the assignee, use the `principalId` you copied in step 1.

```azurecli
az role assignment create --assignee <managedIdentityprincipalId> \
    --scope /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName> \
    --role "<roleName>" 
```

To get the role names that a service principal can be assigned to, use the [az role definition list](/cli/azure/role/definition#az-role-definition-list) command.

```azurecli
az role definition list \
    --query "sort_by([].{roleName:roleName, description:description}, &roleName)" \
    --output table
```

For example, to allow the managed identity with the ID of `aaaaaaaa-bbbb-cccc-1111-222222222222` read, write, and delete access to Azure Storage blob containers and data in all storage accounts in the *msdocs-python-sdk-auth-example* resource group in the subscription with ID `aaaa0a0a-bb1b-cc2c-dd3d-eeeeee4e4e4e`, you would assign the application service principal to the *Storage Blob Data Contributor* role using the following command.

```azurecli
az role assignment create --assignee aaaaaaaa-bbbb-cccc-1111-222222222222 \
    --scope /subscriptions/aaaa0a0a-bb1b-cc2c-dd3d-eeeeee4e4e4e/resourceGroups/msdocs-python-sdk-auth-example \
    --role "Storage Blob Data Contributor"
```

For information on assigning permissions at the resource or subscription level using the Azure CLI, see the article [Assign Azure roles using the Azure CLI](/azure/role-based-access-control/role-assignments-cli).

### [Azure portal](#tab/azure-portal)

| Instructions    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Assign managed identity to role step 1](<../../../includes/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-1.md>)] | :::image type="content" source="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-1-240px.png" alt-text="A screenshot showing how to use the top search bar in the Azure portal to locate and navigate to a resource group in Azure." lightbox="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-1.png"::: |
| [!INCLUDE [Assign managed identity to role step 2](<../../../includes/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-2.md>)] | :::image type="content" source="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-2-240px.png" alt-text="A screenshot showing the location of the Access control (I A M ) menu item in the left-hand menu of an Azure resource group." lightbox="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-2.png"::: |
| [!INCLUDE [Assign managed identity to role step 3](<../../../includes/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-3.md>)] | :::image type="content" source="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-3-240px.png" alt-text="A screenshot showing how to navigate to the role assignments tab and the location of the button used to add role assignments to a resource group." lightbox="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-3.png"::: |
| [!INCLUDE [Assign managed identity to role step 4](<../../../includes/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-4.md>)] | :::image type="content" source="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-4-240px.png" alt-text="A screenshot showing how to filter and select role assignments to be added to the resource group." lightbox="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-4.png"::: |
| [!INCLUDE [Assign managed identity to role step 5](<../../../includes/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-5.md>)] | :::image type="content" source="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-5-240px.png" alt-text="A screenshot showing how to select managed identity as the type of user you want to assign the role (permission) on the add role assignments page." lightbox="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-5.png"::: |
| [!INCLUDE [Assign managed identity to role step 6](<../../../includes/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-6.md>)] | :::image type="content" source="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-6-240px.png" alt-text="A screenshot showing how to use the select managed identities dialog to filter and select the managed identity to assign the role to." lightbox="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-6.png"::: |
| [!INCLUDE [Assign managed identity to role step 7](<../../../includes/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-7.md>)] | :::image type="content" source="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-7-240px.png" alt-text="A screenshot of the final add role assignment screen where a user needs to select the Review + Assign button to finalize the role assignment." lightbox="../../../includes/media/sdk-auth-passwordless/assign-managed-identity-to-role-azure-portal-7.png"::: |

---

## 3 - Implement DefaultAzureCredential in your application

When your code is running in Azure and managed identity has been enabled on the Azure resource hosting your app, the [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential) determines the credentials to use in the following order:

1. Check the environment for a service principal as defined by the environment variables `AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, and either `AZURE_CLIENT_SECRET` or `AZURE_CLIENT_CERTIFICATE_PATH` and (optionally) `AZURE_CLIENT_CERTIFICATE_PASSWORD`.
1. Check keyword parameters for a user-assigned managed identity. You can pass in a user-assigned managed identity by specifying its client ID in the `managed_identity_client_id` parameter.
1. Check the `AZURE_CLIENT_ID` environment variable for the client ID of a user-assigned managed identity.
1. Use the system-assigned managed identity for the Azure resource if it's enabled.

You can exclude managed identities from the credential by setting the `exclude_managed_identity_credential` keyword parameter `True`.

In this article, we're using the system-assigned managed identity for an Azure App Service web app, so we don't need to configure a managed identity in the environment or pass it in as a parameter. The following steps show you how to use `DefaultAzureCredential`.

First, add the `azure.identity` package to your application.

```terminal
pip install azure-identity
```

Next, for any Python code that creates an Azure SDK client object in your app, you'll want to:

1. Import the `DefaultAzureCredential` class from the `azure.identity` module.
1. Create a `DefaultAzureCredential` object.
1. Pass the `DefaultAzureCredential` object to the Azure SDK client object constructor.

An example of these steps is shown in the following code segment.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

# Acquire a credential object
token_credential = DefaultAzureCredential()

blob_service_client = BlobServiceClient(
        account_url="https://<my_account_name>.blob.core.windows.net",
        credential=token_credential)
```

As discussed in the [Azure SDK for Python authentication overview](./overview.md) article, `DefaultAzureCredential` supports multiple authentication methods and determines the authentication method being used at runtime. The benefit of this approach is that your app can use different authentication methods in different environments without implementing environment-specific code. When the preceding code is run on your workstation during local development, `DefaultAzureCredential` will use either an application service principal, as determined by environment settings, or developer tool credentials to authenticate with other Azure resources. Thus, the same code can be used to authenticate your app to Azure resources during both local development and when deployed to Azure.
