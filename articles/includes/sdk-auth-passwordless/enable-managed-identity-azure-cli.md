---
ms.topic: include
ms.custom: devx-track-azurecli
ms.date: 03/31/2022
---
#### [Azure App Service](#tab/azure-app-service)

```azurecli
az webapp identity assign --resource-group <resource-group-name> --name <web-app-name>
```

#### [Azure Virtual Machines](#tab/azure-virtual-machines)

```azurecli
az vm identity assign --resource-group <resource-group-name> --name <virtual-machine-name>
```

---
