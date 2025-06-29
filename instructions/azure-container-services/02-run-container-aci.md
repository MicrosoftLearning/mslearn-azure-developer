---
lab:
    topic: Azure container services
    title: 'Deploy a container to Azure Container Instances using Azure CLI commands'
    description: 'Learn how to use Azure CLI commands to deploy a container to Azure Container Instances.'
---

# Deploy a container to Azure Container Instances using Azure CLI commands

In this exercise, you deploy and run a container in Azure Container Instances (ACI) using Azure CLI. You learn how to create a container group, specify container settings, and verify that your containerized application is running in the cloud.

Tasks performed in this exercise:

* Create Azure Container Instance resources in Azure
* Create and deploy a container
* Verify the container is running

This exercise takes approximately **15** minutes to complete.

## Create a resource group

1. In your browser navigate to the Azure portal [https://portal.azure.com](https://portal.azure.com); signing in with your Azure credentials if prompted.

1. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new cloud shell in the Azure portal, selecting a ***Bash*** environment. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal.

    > **Note**: If you have previously created a cloud shell that uses a *PowerShell* environment, switch it to ***Bash***.

1. Create a resource group for the resources needed for this exercise. Replace **myResourceGroup** with a name you want to use for the resource group. You can replace **eastus** with a region near you if needed. If you already have a resource group you want to use, proceed to the next step.

    ```
    az group create --location eastus --name myResourceGroup
    ```

## Create and deploy a container

You create a container by providing a name, a Docker image, and an Azure resource group to the **az container create** command. You expose the container to the Internet by specifying a DNS name label.

1. Run the following command to create a DNS name used to expose your container to the Internet. Your DNS name must be unique, run this command from Cloud Shell to create a variable that holds a unique name.

    ```bash
    DNS_NAME_LABEL=aci-example-$RANDOM
    ```

1. Run the following command to create a container instance. Replace **myResourceGroup** and **myLocation** with the values you used earlier. It takes a few minutes for the operation to complete.

    ```bash
    az container create --resource-group myResourceGroup 
        --name mycontainer 
        --image mcr.microsoft.com/azuredocs/aci-helloworld 
        --ports 80 
        --dns-name-label $DNS_NAME_LABEL --location myLocation
        --os-type Linux
        --cpu 1
        --memory 1.5 
    ```

    In the previous command, **$DNS_NAME_LABEL** specifies your DNS name. The image name, **mcr.microsoft.com/azuredocs/aci-helloworld**, refers to a Docker image that runs a basic Node.js web application.

Move to the next section after the **az container create** command is finished.

## Verify the container is running

You can check the containers build status with the **az container show** command. 

1. Run the following command to check the provisioning status of the container you created. Replace **myResourceGroup** with the value you used earlier.

    ```bash
    az container show --resource-group myResourceGroup 
        --name mycontainer 
        --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" 
        --out table 
    ```

    You see your container's fully qualified domain name (FQDN) and its provisioning state. Here's an example.

    ```
    FQDN                                    ProvisioningState
    --------------------------------------  -------------------
    aci-wt.eastus.azurecontainer.io         Succeeded
    ```

    > **Note:** If your container is in the **Creating** state, wait a few moments and run the command again until you see the **Succeeded** state.

1. From a browser, navigate to your container's FQDN to see it running. The image isn't running You may get a warning that the site isn't safe.

## Clean up resources

Now that you finished the exercise, you should delete the cloud resources you created to avoid unnecessary resource usage.

1. Navigate to the resource group you created and view the contents of the resources used in this exercise.
1. On the toolbar, select **Delete resource group**.
1. Enter the resource group name and confirm that you want to delete it.

> **CAUTION:** Deleting a resource group deletes all resources contained within it. If you chose an existing resource group for this exercise, any existing resources outside the scope of this exercise will also be deleted.
