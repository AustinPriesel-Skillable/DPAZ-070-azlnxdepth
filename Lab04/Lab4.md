# Lab 4- Scale Streaming Workloads and Validate AI Nodes

## Scenario

Northwind Media streams live sporting events and entertainment content to viewers worldwide.

To support increasing viewer demand, the platform team plans to modernize the media processing platform using Azure Kubernetes Service (AKS) running on Azure Linux.

Before deploying AI-powered video processing workloads, the team must:
- Create an Azure Linux AKS cluster.
- Validate Azure Linux worker nodes.
- Expand cluster capacity using Azure Linux node pools.
- Deploy a media-processing application.
- Verify workload execution on Azure Linux nodes.


### Objectives

After completing this lab, you will be able to:
- Create an AKS cluster using Azure Linux.
- Connect to an AKS cluster using kubectl.
- Verify Azure Linux node operating systems.
- Add Azure Linux node pools.
- Deploy containerized media workloads.
- Validate workload placement on Azure Linux nodes.


## Exercise 1: Create an Azure Linux AKS Cluster

### Task 1: Create Azure Linux AKS Cluster

1. Open a browser and navigate to +++https://portal.azure.com+++ and sign with Azure credentials.

1. Open Cloud Shell and select Bash

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image1.png)

1. Select the subscription and click on Apply

1. Run below commands to set env variables

    ```
    export RESOURCE_GROUP="ResourceGroup1"

    export CLUSTER_NAME="broadcastaks"
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image2.png)

1. Run below command to create Azure Linux AKS Cluster

    `az aks create --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME --os-sku AzureLinux --node-count 3 --generate-ssh-keys`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image3.png)

1. Wait for deployment to finish. Review the output. Locate osSku

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image4.png)


## Exercise 2: Connect and Validate Azure Linux Nodes

### Task 1: Configure kubectl

1. Run below command to get aks credentials.

    `az aks get-credentials --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image5.png)

1. Run below command to view cluster nodes status

    `kubectl get nodes`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image6.png)

1. Run below command to verify Azure Linux node OS and locate OS-IMAGE

    `kubectl get nodes -o wide`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image7.png)

1. Run below command to verify pool configuration.Make sure cluster got created , Azure linux nodes avaialble

    `az aks nodepool list --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --query "[].{Pool:name,OS:osSku,Image:nodeImageVersion}" -o table`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image8.png)


## Exercise 3: Expand AI Processing Capacity

### Task 1: Create Additional Azure Linux Node Pool

1. Run below command to create additional node pool

    `az aks nodepool add --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --name mediapool --node-count 3 --os-sku AzureLinux`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image9.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image10.png)

1. Run below command to review node pools

    `az aks nodepool list --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --query "[].{Pool:name,OS:osSku}" -o table`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image11.png)

1. Run below command to verify node pool and check system node pool exists, media processing node pool exists . Review the increased node count.

    `kubectl get nodes`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image12.png)


## Exercise 4: Deploy a Media Processing Application

### Task 1: Create Deployment Manifest

1. Create **media-analyzer.yaml** and add the code.

    `vi media-analyzer.yaml`

    ```
    apiVersion: apps/v1

    kind: Deployment

    metadata:

    name: media-analyzer

    spec:

    replicas: 3

    selector:

    matchLabels:

    app: media-analyzer

    template:

    metadata:

    labels:

    app: media-analyzer

    spec:

    containers:

    - name: media-analyzer

    image: nginx

    ports:

    - containerPort: 80

    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image13.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image14.png)

1. Press **Esc** and enter `:wq` and then press Enter to save the file

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image15.png)

1. Run the below command to deploy the application. deployment.apps/media-analyzer gets created

    `kubectl apply -f media-analyzer.yaml`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image16.png)

1. Run below command to verify pods status and make sure they are running

    `kubectl get pods`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image17.png)

1. Run below command to check pod placement.review node and column

    `kubectl get pods -o wide`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab04/media/image18.png)


## Summary

In this lab, you built the foundation of a media processing platform using Azure Linux and AKS.

You:
- Created an Azure Linux AKS cluster.
- Connected to the cluster using kubectl.
- Verified Azure Linux node operating systems.
- Added an Azure Linux node pool.
- Deployed a containerized media-processing application.
- Validated workload execution on Azure Linux nodes.


This demonstrates how Azure Linux supports scalable media processing workloads on Azure Kubernetes Service.
