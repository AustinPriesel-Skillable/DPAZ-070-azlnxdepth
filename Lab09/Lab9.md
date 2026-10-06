# Lab 9 - Create and Publish a Governed Azure Linux Image

## Scenario

Northwind Manufacturing manages factory-control systems running on Azure Linux.

Before a Linux image is approved for factory deployment, administrators must:
- Apply system updates.
- Validate repositories.
- Review logs.
- Verify storage and filesystem health.
- Publish a governed image.


### Objectives

After completing this lab, you will be able to:
- Manage packages with DNF5.
- Review Azure Linux repositories.
- Monitor storage and filesystem health.
- Review logs using journalctl.
- Configure log retention.
- Publish a governed Azure Linux image.


## Exercise 1: Manage Packages Using DNF5

1. Open a browser and go to **https:\\portal.azure.com** and sign in with your Azure credentials. Select virtual machines tile on the home page.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image1.png)

1. Click on **Create- \> Virtual machine**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image2.png)

1. Configure Azure Linux VM with below details

    - Resource Group : ResourceGroup1
    - VM Name: `manufacturingvm`
    - Region: @lab.CloudResourceGroup(ResourceGroup1).Location
    - Availability options : No infrastructure redundancy required
    - Security type : Standard
    - Image : See all images-\> Search Azure linux -\> Select Azure linux

    - Key pair name : `manufacturingvm_key`
    - **Review+ Create**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image3.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image4.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image5.png)


1. Once the validation is passed click on **Create**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image6.png)

1. Click on **Download private key and create resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image7.png)

1. Wait for the deployment to complete , click on **Go to resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image8.png)

1. Copy the public IP address.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image9.png)

1. Open Visual Studio code -\> Terminal -\> New Terminal -\> Git Bash

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image10.png)

1. **Set permissions on the SSH key.** For macOS or Linux:

    `chmod 400 ~/Downloads/manufacturingvm_key.pem`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image11.png)

1. **Connect to the VM.**Replace *{PUBLIC_IP}* with the public IP address returned during VM creation. If prompted to trust the host, type yes and press Enter.

    `ssh -i ~/Downloads/manufacturingvm_key azureuser@{PUBLIC_IP}`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image12.png)

1. Run below command to install updates.

    `sudo dnf update -y`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image13.png)

1. Run below command to review repositories.

    `dnf repolist`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image14.png)

1. Review package information by running below command and oberserve version, vendor and repository.

    `dnf info openssl`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image15.png)


## Exercise 2: Review Storage Health

1. Run command to display storage usage.

    `df -h`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image16.png)

1. Run below command to display filesystem type.

    `df -T /`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image17.png)

1. Run below commands and review block devices and file system identifiers

    `lsblk`

    `blkid`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image18.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image19.png)


## Exercise 3: Review Networking Configuration

1. Run below command to review ip config and routes

    `ip addr`

    `ip route`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image20.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image21.png)

1. Review network service and check interface status by running below commands.

    `sudo systemctl status systemd-networkd`

    `networkctl list`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image22.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image23.png)


## Exercise 4: Review Logging and Monitoring

1. Review system logs and SSH service logs.

    `journalctl`

    `journalctl -u sshd`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image24.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image25.png)

1. Review real-time logs

    `journalctl -f`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image26.png)

1. Review system messages.Press Ctrl + C after review to stop the log messages.

    `sudo tail -f /var/log/messages`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image27.png)


## Exercise 5: Configure Log Retention Policy

1. Run below command to create sample log.

    `sudo touch /var/log/factory.log`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image28.png)

1. Create logrotate policy and add below code to it.

    `sudo vi /etc/logrotate.d/factoryapp`

    ```
    /var/log/factory.log {

    weekly

    rotate 4

    compress

    missingok

    notifempty

    }
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image29.png)

1. Save the file – press Esc and enter :wq and press Enter

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image30.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image31.png)

1. Validate configuration by running below command review the output.

    `sudo logrotate -d /etc/logrotate.d/factoryapp`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image32.png)


## Exercise 6: Publish Governed Azure Linux Image

1. Return to Azure Portal. Search +++Azure Compute Gallery+++ and select it

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image33.png)

1. Click on Create

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image34.png)

1. Create the gallery with below details and then click on **Review + create.**

    - Resource Group : ResourceGroup1
    - Gallery Name- +++manufacturingGallery+++
    - Region : @lab.CloudResourceGroup(ResourceGroup1).Location

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image35.png)


1. Once the validation passed, click on Create.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image36.png)

1. Wati for the deployment to complete and then click on **Go to resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image37.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image38.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image39.png)

1. Open **manufacturingvm and** Stop it. Wait until Stopped (Deallocated)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image40.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image41.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image42.png)

1. Select **Capture-\>Image**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image43.png)

1. Create Image Definition.

    - Target Azure compute gallery : **manufacturingGallery**
    - Target VM image definition : Create new
    - Image Name: `ManufacturingSecureImage`
    - Publisher:`Northwind`
    - SKU: `v1`
    - Version: `1.0.0`
    - Select:Review + Create

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image44.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image45.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image46.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image47.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image48.png)


1. Once the validation passed, click on Create.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image49.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab09/media/image50.png)


## Summary

In this lab, you performed the activities required to prepare and govern an Azure Linux image for manufacturing environments. You provisioned an Azure Linux virtual machine and used **DNF5** to update the operating system, review package repositories, and inspect installed packages. You then examined system administration components by validating storage utilization, filesystem types, block devices, networking configuration, and network services. To support operational monitoring, you reviewed system logs using **journalctl**, monitored SSH and system events, and configured a **log rotation policy** to enforce log retention and prevent uncontrolled log growth. Finally, you implemented image governance by creating an **Azure Compute Gallery**, capturing the configured Azure Linux virtual machine, creating an image definition and version, and publishing a standardized image named +++ManufacturingSecureImage+++. By completing this lab, you learned how to maintain Azure Linux systems, manage packages and repositories, monitor system health, implement log retention, and create reusable governed images that can be deployed consistently across manufacturing environments.
