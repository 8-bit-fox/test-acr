## Steps for automating image builds
1. Run 
    ```sh
    az login
    ```
   to login the Azure CLI with your account.
1. Create a Terraform sheet
    ```hcl-terraform
    provider "azurerm" {
      version = "=1.44.0"
      subscription_id = ${SUBSCRIPTION_ID}
    }
    
    resource "azurerm_resource_group" "rg" {
      location = ${LOCATION}
      name = ${NAME}
    }
    
    resource "azurerm_container_registry" "acr" {
      name                     = ${NAME}
      resource_group_name      = azurerm_resource_group.rg.name
      location                 = azurerm_resource_group.rg.location
      sku                      = "Basic"
    }```
1.  Run
    ```shell script
    terraform apply
    ```
   to deploy an ACR. 
1. Create a personal access token (e.g., Github) and save it in a file `access.token`.
1. Run
    ```shell script
    az acr task create --name hello-world-task -f hello-world-task.yaml -c https://github.com/marquardts/test-acr.git --git-access-token `cat access.token`
    ```
   to create a task to execute the task definition in `hello-world-task.yaml` from the repo. By default, on-commit and base-image change triggers are active.

## Caveats
- Does not work with Github Enterprise
   
## Sources
- https://docs.microsoft.com/en-us/azure/container-registry/container-registry-tutorial-build-task
- https://docs.microsoft.com/en-us/azure/container-registry/container-registry-tasks-reference-yaml
- https://www.terraform.io/docs/providers/azurerm/r/container_registry.html
