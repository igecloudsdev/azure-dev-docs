---
title: Build a containerized Python web app in Azure Container Registry
description: Build a containerized Python web app (Django or Flask) in Azure Container Registry, without the need to install Docker locally.
ms.topic: conceptual
ms.date: 03/31/2025
ms.custom: devx-track-python, py-fresh-zinc, devx-track-azurecli
---

# Build a containerized Python web app in Azure

In this article, you learn how to build a containerized Python web app directly in [Azure Container Registry](/azure/container-registry/container-registry-intro) without installing Docker locally. Building the Docker image in Azure is often faster and easier than creating the image locally and then pushing it to the Azure Container Registry. Additionally, cloud-based image building eliminates the need for Docker to run in your development environment.

App Service enables you to run containerized web apps and deploy them through the continuous integration/continuous deployment (CI/CD) capabilities of Docker Hub, Azure Container Registry, and Visual Studio Team Services. This article is part 3 of a 5-part tutorial series about how to containerize and deploy a Python web app to Azure App Service. In this part of the tutorial, you learn how to build the containerized Python web app in Azure.

Azure App Service lets you deploy and run containerized web apps leveraging CI/CD pipelines from platforms like Docker Hub, Azure Container Registry, and Azure DevOps. This article is part 3 of a 5-part tutorial series.

In [part 2 of this tutorial](tutorial-containerize-deploy-python-web-app-azure-02.md) series, you built and ran the container image locally. In contrast, in this part of the tutorial, you build (containerize) the same Python web app directly into a Docker image in the [Azure Container Registry](/azure/container-registry/container-registry-intro). Building the image in Azure is typically faster and easier than building locally and then pushing the image to a registry. Also, building in the cloud doesn't require Docker to be running in your dev environment.

Once the Docker image is in Azure Container Registry, it can be deployed to Azure App service.

This service diagram highlights the components covered in this article.

:::image type="content" source="./media/tutorial-container-web-app/containerization-of-python-apps-build-cloud.png" alt-text="A screenshot of the services using in the Tutorial - Containerized Python App on Azure with the build-in-cloud path highlighted." lightbox="./media/tutorial-container-web-app/containerization-of-python-apps-build-cloud.png":::

## Create an Azure Container Registry

If you have an existing Azure Container Registry you wish to use, skip this next step and proceed to the next step. Otherwise, create a new Azure Container Registry.

### [Azure CLI](#tab/azure-cli)

Azure CLI commands can be run in the [Azure Cloud Shell](https://shell.azure.com/) or in your local development environment with the [Azure CLI installed](/cli/azure/install-azure-cli).

> [!NOTE]
> Use the same names as in part 2 of this tutorial series.

1. Create an Azure container registry with the [az acr create](/cli/azure/acr#az-acr-create) command.

    ```azurecli-interactive
    #!/bin/bash
    RESOURCE_GROUP_NAME='<your resource group name>'
    # REGISTRY_NAME must be unique within Azure and contain 5-50 alphanumeric characters.
    REGISTRY_NAME='<your Azure Container Registry name>'

    echo "Creating Azure Container Registry $REGISTRY_NAME..."
    az acr create -g $RESOURCE_GROUP_NAME -n $REGISTRY_NAME --sku Standard
    ```

    ```azurecli-interactive
    # PowerShell syntax
    $RESOURCE_GROUP_NAME='<your resource group name>'
    # REGISTRY_NAME must be unique within Azure and contain 5-50 alphanumeric characters.
    $REGISTRY_NAME='<your Azure Container Registry name>'

    Write-Output "Creating Azure Container Registry $REGISTRY_NAME..."
    az acr create -g $RESOURCE_GROUP_NAME -n $REGISTRY_NAME --sku Standard
    ```

    In the JSON output of the command, locate the `loginServer` value. This represents the fully qualified registry name (all lowercase) and contains the registry name.

1. If you're using the Azure CLI on your local machine, execute the [az acr login](/cli/azure/acr#az-acr-login) command to log in to the container registry.

    ```azurecli-interactive
    az acr login -n $REGISTRY_NAME
    ```

    The command adds "azurecr.io" to the name to create the fully qualified registry name. If successful, you see the message "Login Succeeded".

    > [!NOTE]
    > In the Azure Cloud Shell, the az `acr login command` is not necessary, as authentication is handled automatically through your Cloud Shell session. However, if you encounter authentication issues, you can still use it.

### [VS Code](#tab/vscode-aztools)

> [!IMPORTANT]
> The steps in this section assume that you previously completed the **VS Code** sections of part 2 of this tutorial series.

1. In the Docker extension, go to **REGISTRIES** and connect to Azure.
1. In Visual Studio Code, select **F1** or **CTRL+SHIFT+P** to open the command palette. Then type "registry" and select the **Azure Container Registry: Create Registry** task.

    Alternatively, in the Docker extension **REGISTRIES** section, right-click your subscription, and select **Create Registry**. This action starts the same create registry task.

1. Follow the prompts and enter the following values:

    * **Registry name**: The registry name must be unique within Azure, and contain 5-50 alphanumeric characters.

    * **SKU**: Select **Standard**.

    * **Resource group**: Use your existing resource group.

1. In the Docker extension, in the **REGISTRIES** section, find the registry you created, right-click, and select **View Properties**.

    Look for the `loginServer` key value pair in the output. The value is the fully qualified name of the registry.

### [Azure portal](#tab/azure-portal)

Follow these steps to create a new Azure Container Registry in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), search for "container registry" and select **Container Registry** under **Marketplace** in the results.

1. Under the **Basics** tab on the **Create container registry** form, enter the following values:

    * **Registry name**: The registry name must be unique within Azure, and contain 5-50 alphanumeric characters.
    * **Location**: Use the location that you used in part 2 of this tutorial series.
    * **SKU**: Select **Standard**.

    :::image type="content" source="./media/tutorial-container-web-app/portal-create-registry-form.png" lightbox="./media/tutorial-container-web-app/portal-create-registry-form.png" alt-text="A screenshot showing how to specify a new registry in Azure portal." :::

    When you're finished, select **Review + create**. After the validation is complete, select **Create**.

1. After the deployment completes, click **Go to resource** to view the fully qualified name for the new registry on the **Overview tab**.

1. Copy the **Login server** value. It should be a fully qualified name with "azurecr.io".

    :::image type="content" source="./media/tutorial-container-web-app/portal-create-registry-login-server.png" lightbox="./media/tutorial-container-web-app/portal-create-registry-login-server.png" alt-text="A screenshot that shows how to find the login server value for the registry in Azure portal." :::

1. The admin account is required to deploy a container image from a registry to Azure Web Apps for Containers.

    1. On the **service menu**, click **Settings**, and then select **Access Keys**.
    1. Select **Enabled** for the **Admin User**.

    The registry [admin account](/azure/container-registry/container-registry-authentication#admin-account) is needed when you use the Azure portal to deploy a container image. The admin account is only used during the creation of the App Service. After creating the App Service, the managed identity pulls images from the registry, allowing the admin account to be disabled.

---

## Build an image in Azure Container Registry

You can generate the container image directly in Azure through various approaches:

  * The Azure Cloud Shell allows you to construct the image entirely in the cloud, independent of your local environment.
  * Alternatively, you can use VS Code or the Azure CLI to create it in Azure from your local setup, without needing Docker to be running locally.

### [Azure CLI](#tab/azure-cli)

Azure CLI commands can be run in your local development environment with the [Azure CLI installed](/cli/azure/install-azure-cli) or in [Azure Cloud Shell](https://shell.azure.com/).

1. In the consolde, navigate to the root folder for your cloned repository from part 2 of this tutorial series.
1. Build the container image using the [az acr build](/cli/azure/acr#az-acr-build) command.

    ```azurecli-interactive
    az acr build -r $REGISTRY_NAME -g $RESOURCE_GROUP_NAME -t msdocspythoncontainerwebapp:latest .
    ```

1. Confirm the container image was created with the [az acr repository list](/cli/azure/acr/repository#az-acr-repository-list) command.

    ```azurecli-interactive
    az acr repository list -n $REGISTRY_NAME
    ```

### [VS Code](#tab/vscode-aztools)

These steps require that VS Code be opened in the working folder of your web app.

1. In the Docker extension, go to **REGISTRIES** and connect to Azure.

    :::image type="content" source="./media/tutorial-container-web-app/visual-studio-code-build-image-registries.png" lightbox="./media/tutorial-container-web-app/visual-studio-code-build-image-registries.png" alt-text="A screenshot showing how to check that Azure is signed into Docker Extension in Visual Studio Code." :::

1. Select **F1** or **CTRL+SHIFT+P** to open the command palette, type "registry", and select the **Azure Container Registry: Build Image in Azure** task.

    If you don't see the task, make sure that **Azure** appears under **REGISTRIES** in the Docker extension. You can also right-click the *Dockerfile* and select **Build Image in Azure** to run the task.

1. Input the following values and follow the prompts to create the image.

    * **Tag image**: Use the image name "msdocspythoncontainerwebapp:latest".
    * **Registry provider**: Select **Azure**.
    * **Subscription**: Select the subscription you used to create the registry.
    * **Registry**: Select the registry you created or one to which you have access.
    * **Base OS image**: Select **Linux**.

    Check the **OUTPUT** window for progress and information on the build. If you get a credentials error, right-click the registry in the **REGISTRIES** section of the Docker extension and select **Refresh**.

1. Confirm the image in the Azure Container Registry.

    1. In the Docker extension, in the **REGISTRIES** section, find the container image created.

    1. Confirm the name and tag "latest".

    :::image type="content" source="./media/tutorial-container-web-app/visual-studio-code-build-image-confirm.png" lightbox="./media/tutorial-container-web-app/visual-studio-code-build-image-confirm.png" alt-text="A screenshot showing how to confirm the  information to  build container in Azure in Visual Studio Code." :::

### [Azure portal](#tab/azure-portal)

Sign in to the [Azure portal](https://portal.azure.com/) to complete these steps.

1. Build the image in [Azure Cloud Shell](/azure/cloud-shell/overview) with the [az acr build](/cli/azure/acr#az-acr-build) command.

    ```azurecli-interactive
    #!/bin/bash
    RESOURCE_GROUP_NAME='<your resource group name>'
    # REGISTRY_NAME must be unique within Azure and contain 5-50 alphanumeric characters.
    REGISTRY_NAME='<your Azure Container Registry name>'

    az acr build
      -r $REGISTRY_NAME$ \
      -g $RESOURCE_GROUP_NAME$ \
      -t msdocspythoncontainerwebapp:latest \
      <repo-path>
    ```

    ```azurecli-interactive
    # PowerShell syntax
    $RESOURCE_GROUP_NAME='<your resource group name>'
    # REGISTRY_NAME must be unique within Azure and contain 5-50 alphanumeric characters.
    $REGISTRY_NAME='<your Azure Container Registry name>'

    az acr build
      -r $REGISTRY_NAME$ `
      -g $RESOURCE_GROUP_NAME$ `
      -t msdocspythoncontainerwebapp:latest `
      <repo-path>
    ```

    The last argument in the command is the fully qualified path to the repo. Use https://github.com/Azure-Samples/msdocs-python-django-container-web-app.git for the Django sample app and https://github.com/Azure-Samples/msdocs-python-flask-container-web-app.git for the Flask sample app.

1. Confirm the container image was created with the [az acr repository list](/cli/azure/acr/repository#az-acr-repository-list) command.

    ```azurecli
    az acr repository list -n <registry-name>    ```

---

## Next step

> [!div class="nextstepaction"]
> [Deploy web app](tutorial-containerize-deploy-python-web-app-azure-04.md)
