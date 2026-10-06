# Lab 5- Deploy Multi-Region Fleet Tracking on Azure Linux

## Scenario

Northwind Logistics operates vehicle tracking services across multiple regional distribution centers.

Each new region requires a fleet tracking server that provides:
- Vehicle tracking
- Fleet health monitoring
- Dispatch operations


Currently, administrators manually configure every server, resulting in:
- Configuration drift
- Inconsistent deployments
- Increased administrative effort


To standardize operations, Northwind Logistics will create a reusable Azure Linux image and deploy identical fleet-tracking servers across multiple locations.

### Objectives

After completing this lab, you will be able to:
- Deploy a fleet-tracking application on Azure Linux.
- Create an Azure Compute Gallery.
- Capture and publish Azure Linux images.
- Create image versions.
- Deploy Azure Linux virtual machines from Gallery images.
- Implement a standardized deployment strategy.


## Exercise 1: Deploy a Fleet Tracking Dashboard

### Task 1: Create an Azure Linux Virtual Machine

1. Sign in to the Azure portal - +++https://portal.azure.com+++ and sign in with your Azure credentials.

1. Select Virtual Machines tile .Select: **Create \> Virtual Machine**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image1.png)

1. Configure the VM using the following settings:

    - Resource Group:ResourceGroup1
    - Virtual Machine Name: `fleettrackervm`
    - Region: **@lab.CloudResourceGroup(ResourceGroup1).Location**
    - Availability options : No infrastructure redundancy required
    - Security type : Standard
    - Image: see all images-search Azure Linux and select Azure Linux 

    - Authentication Type: Password
    - Key pair name : `vmKey5`

    - Select **Review + Create**.


1. Select **Create**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image2.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image3.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image4.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image5.png)

1. Review the details and click on **Download private key and create resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image6.png)

1. Wait for deployment to be completed and then click on **Go to resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image7.png)

1. Copy Primary NIC public address to connect VM later in the lab.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image8.png)

1. Open Visual Studio code form Desktop and open Terminal-\>Git Bash

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image9.png)

1. **Set permissions on the SSH key.**

    `chmod 400 ~/Downloads/vmKey5.pem`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image10.png)

1. Update below command with Azure Linux VM public IP and then run

    `ssh -i ~/Downloads/vmKey5.pem azureuser@{PUBLIC_IP}`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image11.png)

1. Run below commands to install required packages on VM.

    Update the VM:

    ```
    sudo tdnf update -y

    sudo tdnf install -y \

    python3 \

    python3-pip \

    git
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image12.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image13.png)

1. Run below command to create app folder.

    `mkdir fleet-tracker`

    `cd fleet-tracker`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image14.png)

1. Create the application file `vi app.py` and add the below code.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image15.png) Add the below code. Save the file: **Esc :wq**

    Press: Enter

    ```
    from flask import Flask

    app = Flask(__name__)

    @app.route("/")

    def home():

    return """

    <h1>Northwind Logistics Fleet Dashboard</h1>

    <h3>Active Vehicles</h3>

    <ul>

    <li>Truck-101 : Bengaluru</li>

    <li>Truck-205 : Chennai</li>

    <li>Truck-307 : Hyderabad</li>

    <li>Truck-410 : Pune</li>

    </ul>

    <p>Status : Operational</p>

    """

    app.run(host="0.0.0.0", port=5000)
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image16.png)

1. **Run below command to** Install Flask:

    `pip3 install --user flask`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image17.png)

1. Start the Fleet Tracking Application.Run- **python3 app.py**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image18.png) 10. Leave the application running.


### Task 7: Configure Network Access

1. Return to Azure Portal. Navigate to fleettrackervm-\>Networking -\>Network Settings.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image19.png)

1. Select **Create port rule -\>Inbound port rule** and configure:

    Destination Port Ranges: `5000`

    Protocol: TCP

    Action: Allow

    Select: Add

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image20.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image21.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image22.png)

1. Open a browser.Navigate to:

    `http://{public-ip}:5000`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image23.png)


## Exercise 2: Create an Azure Compute Gallery

### Task 1: Create a Gallery

1. Search for +++Azure Compute Gallery+++ and select it.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image24.png)

1. Select **Create**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image25.png)

1. Configure with below details and click on **Review + Create.**

    Gallery Name: `fleetGallery`

    Subscription: Current Subscription

    Resource Group: ResourceGroup1

    Region: @lab.CloudResourceGroup(ResourceGroup1).Location

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image26.png)

1. Once validation passed, select **Create**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image27.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image28.png)


## Exercise 3: Create a Golden Fleet Tracking Image

1. Return to the SSH session. Stop the application: Ctrl + C

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image29.png)

1. Navigate to fleettrackervm -\> Overview and select **Stop** and wait until the VM status shows:

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image30.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image31.png)

1. Navigate to fleettrackervm. click on Capture and select Image

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image32.png)

1. Select Target Azure compute gallery : **fleetGallery**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image33.png)

1. Click on **Create** under **Target VM image definition** field

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image34.png)

1. Create an Image Definition with below details and click on OK.

    Image Definition Name: +++leet-tracking-image+++

    Publisher: `Northwind`

    Offer: `FleetTracking`

    SKU: `v1`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image35.png)

1. Create image version with Version: **1.0.0** and then click on **Review + create.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image36.png)

1. Once the validation passed, click on **Create**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image37.png)

1. Wait for image creation to be completed.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image38.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image39.png)

1. Click on **Go to resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image40.png)


## Exercise 4: Deploy a Regional Fleet Tracking Server

Northwind Logistics is opening a new regional dispatch center. Instead of rebuilding a VM manually, administrators will use the image.

1. Click on **Crete VM**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image41.png)

1. Configure VM with below details and then click on Review + Create

    - Resource Group : **ResoruceGroup1**
    - Virtual Machine Name: `fleettracker-east`
    - Region: @lab.CloudResourceGroup(ResourceGroup1).Location
    - Image : **leet-tracking-image**
    - Key paid name : `eastkey`
    - Select inbound ports: 80, 22

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image42.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image43.png)


1. After the validation passed, click on **Create**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image44.png)

1. Click on Download private key and create resource.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image44.png)

1. Wait for the deployment to complete. Click on **Go to resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image45.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image46.png)

1. Copy IP address

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image47.png)

1. Click on **Network-\> Networking Settings -\> Create port tule- \> inbound port rule**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image48.png)

1. Configure rule with below details and click **Add**

    - Destination port range : `5000`
    - Protocol: TCP
    - Name : `eastfleettrack`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image49.png)


1. Switch back to VS code and run below commands to connect to the above vm. **Set permissions on the SSH key.** For macOS or Linux:

    `chmod 400 ~/Downloads/eastKey.pem`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image50.png)

1. **Connect to the VM.**Replace *{PUBLIC_IP}* with the public IP address returned during VM creation. If prompted to trust the host, type yes and press Enter.

    `ssh -i ~/Downloads/eastkey.pem azureuser@{PUBLIC_IP}`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image51.png)

1. Run ls to verify application files are available.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image52.png)

1. Navigate to fleet-tracker and check if app.py file is available

    `cd fleet-tracker`

    `ls`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image53.png)

1. Run below command to start the application.

    `python3 app.py`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image54.png)

1. Browse to: +++http://{regional-vm-ip}:5000+++. Should see Northwind Logistics Fleet Dashboard

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab05/media/image55.png)


## Exercise 5: Deploy a Disaster Recovery Fleet Tracking Server (Optional)

Northwind Logistics requires a backup server for business continuity.

### Task 1: Deploy Another VM

1. Repeat the previous deployment using: fleet-tracking-image

    - Version 1.0.0
    - Configure:
    - Virtual Machine Name:`fleettracker-dr`
    - Deploy the VM.


1. **Connect to the DR Server**

    - Connect via SSH.
    - Verify Azure Linux. Run `cat /etc/os-release`
    - Start Fleet Dashboard.
    - `cd fleet-tracker`
    - `python3 app.py`

1. Open +++http://{dr-vm-ip}:5000+++

1. Compare the following:

    - fleettrackervm
    - fleettracker-east
    - fleettracker-dr

1. Verify all servers display: Northwind Logistics Fleet Dashboard

1. Verify operating system consistency.Run on each VM:

    `cat /etc/os-release`


## Summary

In this lab, you:
- Deployed a fleet tracking application on Azure Linux.
- Created an Azure Compute Gallery.
- Created an image definition and image version.
- Captured a reusable Azure Linux image.
- Deployed regional and disaster recovery fleet-tracking servers from
  the same image.

- Validated standardized deployments across multiple servers.


This demonstrates how Azure Linux image lifecycle management helps logistics organizations deploy consistent, repeatable infrastructure across multiple operational environments.
