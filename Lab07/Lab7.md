# Lab 7 - Migrate Digital Wallet with OS Guard Protection

## Scenario

Northwind Bank operates a digital wallet platform used by customers for mobile payments, peer-to-peer transfers, and online purchases.

The security team has mandated stronger protection for payment-processing infrastructure to meet regulatory and compliance requirements.

To improve workload protection, the platform team will:
- Create a secured Azure Linux AKS cluster.
- Review OS Guard security capabilities.
- Enable hardened node protection features.
- Add a protected node pool.
- Enable cluster monitoring.
- Review migration considerations from OS Guard to Azure Container
  Linux.


### Objectives

After completing this lab, you will be able to:
- Review Azure Linux with OS Guard security features.
- Enable hardened AKS security controls.
- Create and manage secure AKS node pools.
- Enable monitoring for secured workloads.
- Validate security settings.
- Understand the migration path to Azure Container Linux.


## Exercise 1: Prepare the Environment

### Task 1: Open Azure Cloud Shell

1. Open a browser and go to +++https://portal.azure.com+++ and sign in with your Azure subscription.

1. Click on Cloudhsell and select **Bash**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image1.png)

1. Select your subscription and then click on **Apply**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image2.png)

1. Run below command to Install aks-preview Extension

    `az extension add --name aks-preview`

    If already installed:

    `az extension update --name aks-preview`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image3.png)

1. Run below command to register OS Guard preview feature

    `az feature register --namespace Microsoft.ContainerService --name AzureLinuxOSGuardPreview`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image4.png)

1. Run below command to verify registration:

    `az feature show --namespace Microsoft.ContainerService --name AzureLinuxOSGuardPreview`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image5.png)

1. Run below command to register resource provider

    `az provider register --namespace Microsoft.ContainerService`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image6.png)


## Exercise 2: Create a Secure Digital Wallet AKS Cluster

### Task 1: Create Secure Cluster

1. Run below command to set environment variables.

    ```
    export RESOURCE_GROUP="ResourceGroup1"

    export REGION="japaneast"

    export CLUSTER_NAME="walletguardcluster"
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image7.png)

1. Run below command to create secure cluster.

    `az aks create --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME --os-sku AzureLinuxOSGuard --node-osdisk-type Managed --enable-fips-image --enable-secure-boot --enable-vtpm --generate-ssh-keys`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image8.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image9.png)


## Exercise 3: Connect and Validate the Secured Cluster

1. Run below command to get credentials

    `az aks get-credentials --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image10.png)

1. Run below command to verify connection:

    `kubectl get nodes`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image11.png)

1. Review cluster node pool information:

    `az aks nodepool list --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image12.png)


## Exercise 4: Expand Protected Capacity

1. Run below command to add secure node pool

    `az aks nodepool add --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --name osgpool --node-count 3 --os-sku AzureLinuxOSGuard --node-osdisk-type Managed --enable-fips-image --enable-secure-boot --enable-vtpm`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image13.png)

1. Run below command to review node pool

    `az aks nodepool list --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image14.png)


## Exercise 5: Enable Monitoring

1. Run below command to connect to cluster:

    `az aks get-credentials --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image15.png)

1. Run below command to register Microsoft.OperationalInsights

    `az provider register --namespace Microsoft.OperationalInsights`

1. Run below command to enable monitoring:

    `az aks enable-addons --addons monitoring --name $CLUSTER_NAME --resource-group $RESOURCE_GROUP`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image16.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image17.png)

1. Run below command to verify monitoring agent deployment

    `kubectl get ds ama-logs --namespace kube-system`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image18.png)

    5, Run below command to verify monitoring solution:

    `kubectl get deployment ama-logs-rs --namespace kube-system`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab07/media/image19.png)

    >[!Note] Azure Linux with OS Guard is a preview feature and is scheduled for retirement. Organizations should evaluate Azure Container Linux (ACL) as the strategic platform for future AKS deployments.


## Summary

In this lab, you:

Created a secured AKS cluster using Azure Linux with OS Guard

Enabled FIPS, Secure Boot, and vTPM protections

Added a protected node pool

Enabled monitoring using Container Insights

Reviewed security controls used to protect digital wallet workloads

Evaluated migration planning from OS Guard to Azure Container Linux

This demonstrates how regulated industries such as banking can harden Azure Linux environments while preparing for future adoption of Azure Container Linux.
