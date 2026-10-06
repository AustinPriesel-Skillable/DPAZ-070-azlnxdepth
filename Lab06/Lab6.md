# Lab 6 - Build Secure Payment Processing with Azure Container Linux

## Scenario

Northwind Bank is modernizing its payment processing platform.

The development team wants to:
- Build a payment API.
- Package the API as a container image.
- Store the image in Azure Container Registry.
- Deploy the application onto an Azure Container Linux (ACL) based AKS
  cluster.

- Enable monitoring and validate secure operations.


### Objectives

After completing this lab, you will be able to:
- Create and configure an Azure Linux development environment.
- Develop and test a secure payment processing API using Python and
  Flask.

- Containerize an application using Docker.
- Build and manage container images.
- Create and configure Azure Container Registry (ACR).
- Push container images to Azure Container Registry.
- Deploy and validate an Azure Container Linux (ACL) based AKS cluster.
- Deploy containerized workloads to Azure Kubernetes Service (AKS).
- Publish applications using Kubernetes Services.
- Enable monitoring and validate secure operations in an Azure Container
  Linux environment.


## Exercise 1: Create an Azure Linux Development VM

### Task 1: Create Azure Linux VM

1. Opena browser and enter +++https://portal.azure.com+++ and sign in with your Azure credentials.

1. Search for +++Virtual Machines+++ and select it.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image1.png)

1. Select **Create → Azure Virtual Machine**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image2.png)

1. Enter below details and create VM.

    - Resource Group: ResourceGroup1
    - Virtual Machine Name: `bankdevvm`
    - Region: @lab.CloudResourceGroup(ResourceGroup1).Location
    - Availability options : **No infrastructure redundancy required.**
    - Security Type : Standard
    - Image: **See all image-\> Search `Azure Linux` and select Azure Linux
    - Create Key pair
    - Key Pair name : `myKey`
    - **Review + Create**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image3.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image4.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image5.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image6.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image7.png)


1. After the validation passed, click on **Create**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image8.png)

1. Click on **Download private key and create resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image9.png)

1. Wait for the deployment to complete and then click on **Go to resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image10.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image11.png)

1. Copy Primary **NIC public IP address** and save it in notepad to connect to VM.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image12.png)


### Task 2: Connect to VM

1. Open **VS code -\> Terminal**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image13.png)

1. Open **Git Bash** terminal

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image14.png)

1. **Set permissions on the SSH key.** For macOS or Linux:

    `chmod 400 ~/Downloads/myKey.pem`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image15.png)

1. **Connect to the VM.**Replace {PUBLIC_IP} with the public IP address returned during VM creation. If prompted to trust the host, type yes and press Enter.

    `ssh -i ~/Downloads/myKey.pem azureuser@{PUBLIC_IP}`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image16.png)


### Task 3: Install Development Tools

1. Run below command to update packages.

    `sudo tdnf update -y Install Python and pip`

    `sudo tdnf install -y python3 python3-pip git`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image17.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image18.png)

1. Run below command to verify python versions in Azure Linux VM.

    `python3 --version`

    `pip3 --version`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image19.png)


## Exercise 2: Create Secure Payment API

### Task 1: Create Application

1. Open new instance of GitBash and run below command to create folder.

    `mkdir payment-api`

    `cd payment-api`

    `pwd`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image20.png)

1. Click on File- \> Open Folder and open the **payment-api folder**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image21.png)

1. Create app.py . copy the below code and save the file

    ```
    from flask import Flask, jsonify
    app = Flask(__name__)
    @app.route("/")
    def home():
    return "Northwind Bank Secure Payment API"
    @app.route("/payment")
    def payment():
    return jsonify({
    "transactionId": "TXN10001",
    "cardNumber": "****1234",
    "merchant": "Northwind Retail",
    "amount": "5000",
    "currency": "INR",
    "status": "Approved"
    })
    app.run(host="0.0.0.0", port=5000)
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image22.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image23.png)

### Task 3: Install Flask and run the application

1. Open New Terminal and select Git Bash

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image24.png)

1. Run below command

    `pip3 install --user flask`

    `python -m pip show flask`

    `python3 app.py`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image25.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image26.png)

1. Click on the app link

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image27.png)

1. App open in browser.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image28.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image29.png)

### Task 5: Validate Payment Endpoint

1. Open new instance second SSH session and run blow curl command.

    `curl <http://localhost:5000/payment>`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image30.png)

1. Switch back first instance stop application: Ctrl+C

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image31.png)

## Exercise 3: Containerize the Payment API

Build this portion on the machine where Docker is available.

### Task 1: Create Dockerfile ,Requirements File

1. Run below command to create requirements file

    `echo "flask" > requirements.txt`

    `cat requirements.txt`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image32.png)

1. Create dockerfile `vi Dockerfile` and add the below code

    ```
    FROM python:3.12-slim

    WORKDIR /app

    COPY requirements.txt .

    RUN pip install -r requirements.txt

    COPY app.py .

    EXPOSE 5000

    CMD ["python","app.py"]
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image33.png)

1. Press Esc, `:wq` to save the file.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image34.png)![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image35.png)  

### Task 4: Build Container Image

1. Double click on the Docker icon from desktop.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image36.png)

1. Click on **Accept**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image37.png)

1. Click on skip.

1. Click on **Skip** on Welcome to Docker page.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image38.png)

1. Make sure the Docker is running.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image39.png)

1. Switch back to Vs code. Run below command to build container image

    `docker build -t payment-api:v1 .`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image40.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image41.png)

### Task 5: Verify Image

1. Run below command to check

    `docker images`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image42.png)

### Task 6: Run Container

1. Run below command to run the container.

    `docker run -d -p 5000:5000 payment-api:v1`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image43.png)

### Task 7: Validate Container

1. Run below command to check the app.

    `curl <http://localhost:5000/payment>`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image44.png)

## Exercise 4: Create Azure Container Registry

### Task 1: Create Container Registry

1. Switch back Azure portal and open Cloud shell.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image45.png)

1. Select **Bash**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image46.png)

1. Select subscription and click on **Apply**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image47.png)

1. Run below command set environment variable

    ```
    export RESOURCE_GROUP=ResourceGroup1

    export ACR_NAME=bankacr12345

    export ACR_NAME="bankacr12345"
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image48.png)

1. Run below command to create container registry.

    `az acr create --resource-group $RESOURCE_GROUP --name $ACR_NAME --sku Basic`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image49.png)

1. Run below command to enable administrator.

    `az acr update --name $ACR_NAME --admin-enabled true`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image50.png)

1. Run below command to retrieve registry credentials

    `az acr show --name $ACR_NAME --query loginServer -o tsv`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image51.png)

1. Run below command to get acr user name and password

    `az acr credential show --name $ACR_NAME --query username -o tsv`

    `az acr credential show --name $ACR_NAME --query passwords[0].value -o tsv`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image52.png)

    >[!Note] Do not run az acr login in Azure Cloud Shell. Cloud Shell doesn't support the Docker daemon. Use Cloud Shell only to obtain registry credentials and perform the Docker login from the Skillable VM where Docker is installed.

1. Switch back to Visual Studio code .Replace \*{*{ACR_NAME}*}* with the
    acr name retrieved from above command and run it. Enter username and
    password when

    `az acr login --name {ACR_NAME}`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image53.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image54.png)

## Exercise 5: Push Application Image into ACR

### Task 1: Retrieve Login Server

1. Run below command to tage the image

    `docker tag payment-api:v1 bankacr12345.azurecr.io/payment-api:v1`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image55.png)

1. **Run below command to push the image**

    `docker push bankacr12345.azurecr.io/payment-api:v1`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image56.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image57.png)

## Exercise 6: Create Azure Container Linux AKS Cluster

1. Run below command to create ACL cluster.

    ```
    export CLUSTER_NAME=bankaclcluster

    export RESOURCE_GROUP=ResourceGroup1
    ```

    `az login`

    `az aks create --resource-group $RESOURCE_GROUP --name bankaclcluster --os-sku AzureLinux --node-count 3 --generate-ssh-keys`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image58.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image59.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image60.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image61.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image62.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image63.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image64.png)

## Exercise 7: Connect and Validate Azure Linux Nodes

### Task 1: Validate ACL nodes

1. Run below command to get acl credentials

    `az aks get-credentials --resource-group $RESOURCE_GROUP --name bankaclcluster`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image65.png)

1. Run below command to get nodes. Review node information.

    `kubectl get nodes -o wide`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image66.png)

1. Run below command to validate node pool OS

    `az aks nodepool list --resource-group $RESOURCE_GROUP --cluster-name bankaclcluster --query "[].{Pool:name,OS:osSku,Image:nodeImageVersion}" -o table`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image67.png)

## Exercise 8: Add Azure Container Linux Node Pool

### Task 1: Add node pool

1. **Run below command to add nodepool**

    `az aks nodepool add --resource-group $RESOURCE_GROUP --cluster-name bankaclcluster --name securepool --node-count 3 --os-sku AzureLinux`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image68.png)

## Exercise 9: Deploy Payment Application

### Task 1: Attach Registry

1. Run below command to get acr name

    `az acr repository list --name $ACR_NAME --output table`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image69.png)

1. Run below command attach acr (Require owner role)

    `$ az aks update --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME -attach-acr $ACR_NAME`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image70.png)

### Task 2: Create Deployment Manifest

1. Create a file `payment-deployment.yaml` and add the below
    code. Update image name as appropriate.

    ```
    apiVersion: apps/v1

    kind: Deployment

    metadata:

    name: payment-api

    spec:

    replicas: 3

    selector:

    matchLabels:

    app: payment-api

    template:

    metadata:

    labels:

    app: payment-api

    spec:

    containers:

    - name: payment-api

    image: bankacr12345.azurecr.io/payment-api:v1

    ports:

    - containerPort: 5000
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab06/media/image71.png)

1. Run below command to deploy.

    `kubectl apply -f payment-deployment.yaml`

1. Run below command to verify pods status

    `kubectl get pods`

## Exercise 10: Publish and Monitor the Secure Payment Platform

1. Run the command to expose the service

    `kubectl expose deployment payment-api --port=80 --target-port=5000 --type=LoadBalancer`

1. Run below command t retrieve service.Wait until EXTERNAL_IP appears

    `kubectl get svc`

1. Update the url and run it in browser -http://*{external-ip}*/payment

1. **Run below command to e**nable monitoring:

    `az aks enable-addons --addons monitoring --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME`

1. Run below command to verify monitoring agent:

    `kubectl get ds ama-logs -n kube-system`

1. Verify monitoring deployment:

    `kubectl get deployment ama-logs-rs -n kube-system`

## Summary

In this lab, you modernized a banking payment processing application
using Azure Linux and Azure Container Linux. You started by creating an
Azure Linux development virtual machine and installed the tools required
to build and test a Python-based payment API. You containerized the
application using Docker, built a container image, and stored it in
Azure Container Registry. You then deployed an Azure Container
Linux-based AKS cluster, validated the cluster nodes, and added a
dedicated node pool to support secure containerized workloads. Finally,
you deployed the payment application to Kubernetes, exposed it through a
Load Balancer service, and enabled monitoring to validate operational
health. By completing this lab, you gained hands-on experience with
application modernization, containerization, Azure Container Registry,
Azure Container Linux, Kubernetes deployment, and secure cloud-native
application operations in a banking scenario.
