# Challenge 2

## Create an Azure Kubernetes Service (AKS) from the [Azure Cloud Shell][100]

1. ### Main Setup

   1. **Check default Subscription**

      1. Display available subscriptions

         - `az account list -o table`

      1. **If required**, configure the default subscription

         - `az account set -s NAME_OR_ID`

   1. **Gather location name**

      1. Display in a readable manner the available locations in the US

         - `az account list-locations -o table | grep us`

      1. Store in a local variable the preferred/required location for future resource deployment/access

         - `export LOCATION=eastus`
         - `echo $LOCATION`

   1. **Create usable tags**

      1. Store main tags in a list

         - `TAGS="project=openhacknet team=1 env=dev"`
         - `echo $TAGS`

1. ### Create a Log Analytics Monitor Workspace to be used by the AKS

   1. **Variables setup**

      1. Log Analytics resource group

         - `export LOG_ANALYTICS_WS_RG=log_analytics_workspace_RG`
         - `echo $LOG_ANALYTICS_WS_RG`

      1. Log Analytics Name

         - `export LOG_ANALYTICS_WS_Name=logAnalyticsWST1`
         - `echo $LOG_ANALYTICS_WS_Name`

   1. **Create the Log Analytics Workspace**

      1. Create the Log Analytics Resource group

         `az group create -n $LOG_ANALYTICS_WS_RG -l $LOCATION --tags $TAGS`

      1. Create the Log Analytics Workspace Resource

         `az monitor log-analytics workspace create -n $LOG_ANALYTICS_WS_Name -g $LOG_ANALYTICS_WS_RG -l $LOCATION --tags $TAGS`

1. ### Create the Azure kubernetes Service (AKS)

   1. **Enable aks-preview**

      1. Install aks-preview extension

         `az extension add --name aks-preview`

      <details><summary>Why?</summary>
      <p>

      Some AKS CLI flags are not yet available in the regular az aks, such as node-resource-group, thus we enable the [aks preview][101].

      Check available extensions:

      `az extension list-available --output table`

      [azure-cli-extensions-overview](https://docs.microsoft.com/en-us/cli/azure/azure-cli-extensions-overview?view=azure-cli-latest)

      </p>
      </details>

   1. **Variables setup**

      1. AKS resource group

         - `export AKS_RG=AKS_RG`
         - `echo $AKS_RG`

      1. AKS node resource group (to store required aks resources which are part of the aks cluster, e.g. vms)

         - `export AKS_NODE_RG=AKS_node_RG`
         - `echo $AKS_NODE_RG`

      1. AKS name

         - `export AKS_NAME=AKSCluster`
         - `echo $AKS_NAME`

      1. Get the Workspace Resource id

         - `LOG_ANALYTICS_WS_ID=$(az monitor log-analytics workspace show --workspace-name $LOG_ANALYTICS_WS_Name --resource-group $LOG_ANALYTICS_WS_RG --query id -o tsv)`
         - `echo $LOG_ANALYTICS_WS_ID`

      1. Check available k8s versions to be used

         - `az aks get-versions --location $LOCATION -o yaml`
         - `export K8S_VERSION=1.19.6`
         - `echo $K8S_VERSION`

      1. Node VM Size

         - `NODE_VM_SIZE=Standard_B2s`
         - `echo $NODE_VM_SIZE`

      1. Node Count

         - `NODE_COUNT=3`
         - `echo $NODE_COUNT`

1. ### Create the AKS Service

   1. Create the AKS Resource group

      `az group create -n $AKS_RG -l $LOCATION --tags $TAGS`

   1. Create the AKS Service

      ```bash
      az aks create \
      --name $AKS_NAME \
      --kubernetes-version $K8S_VERSION \
      --resource-group $AKS_RG \
      --node-resource-group $AKS_NODE_RG \
      --vm-set-type VirtualMachineScaleSets \
      --node-vm-size $NODE_VM_SIZE \
      --node-count $NODE_COUNT \
      --enable-addons monitoring \
      --workspace-resource-id $LOG_ANALYTICS_WS_ID \
      --generate-ssh-keys \
      --enable-managed-identity \
      --tags $TAGS
      ```

[100]: https://shell.azure.com
[101]: https://docs.microsoft.com/en-us/cli/azure/ext/aks-preview/aks?view=azure-cli-latest
