# Lab 1 — Provision and Publish an Azure Linux Storefront VM

## Scenario:

You've just joined Northwind Retail as a cloud engineer. Their storefront currently runs on an aging, undocumented Ubuntu VM. Your manager wants a clean, reproducible way to stand up storefront VMs on Azure Linux instead. In this lab you provision one such VM by hand, install the runtime it needs, deploy Northwind's actual storefront application to it, and confirm the whole chain works end to end — exactly the process that gets automated away in Day 2's lab. This same VM carries forward into Labs 2 and 3 today.

### Objective

By the end of the lab, students will be able to:
- Provision an Azure Linux VM.
- Connect to Azure Linux using SSH.
- Install software using Azure Linux package management.
- Deploy a Java web application to Tomcat.
- Configure a MariaDB database.
- Configure application connectivity using environment variables.
- Publish an application through Azure networking.
- Validate a running workload hosted on Azure Linux.


### Task 1 — Provision Azure Linux VM

1. Open browser and navigate to +++https://portal.azure.com+++ and sign in with Azure credentials.

1. Search for +++Virtual Machines+++ and select it

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image1.png)

1. Click on Create and select **Virtual machine.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image2.png)

1. Select below values

    Resource group : **ResourceGroup1**

    Virtual machine name : `vm-storefront-01`

    Region: **East US**

    Availability options : **No infrastructure redundancy required**

    Security type: **Standard**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image3.png)

1. Scroll down in Image field click on **See all images** link

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image4.png)

1. Search for +++Azure Linux+++ and select **Azure Linux 4.0**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image5.png)

1. On Azure Linux tile, click on **Create** drop down and select **Azure Linux 4.0- x64 Gen2**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image6.png)

1. You will be navigated back to Create a virtual machine page with Azure Linux 4.0 image selected .

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image7.png)

1. Enter below values

    Username :`azureuser`

    SSH public key source: **Generate new key pair.**

    Key pair name: `vmKey`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image8.png)

1. Select inbound ports as HTTP(80) , SSH(22) and then click on **Review + Create**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image9.png)

1. Review the details and click on **Create**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image10.png)

1. Click on **Download private key and create resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image11.png)

1. After the deployment successful, click on **Go to resource.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image12.png)

1. On the VM's **Overview** page, copy the **Public IP address**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image13.png)


### Task 2 – Configure Network Access

1. Click on **Networking -\> Network settings** from left navigation menu and click on **Create port rule-\> inbound port rule.**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image14.png)

1. Create an Inbound Security Rule with below values

    - Source: **Any**
    - Source Port Ranges: **\***
    - Destination: **Any**
    - Service: **Custom**
    - Destination Port Ranges: **8080**
    - Protocol: **TCP**
    - Action: **Allow**
    - Priority: `900`
    - Name: `Allow-Tomcat-8080`
    - Select **Add**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image15.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image16.png)


### Task 3 – Connect to the Azure Linux VM

1. Open Visual Studio code and navigate to **Terminal-\> New Terminal**

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image17.png)

1. Open **Git Bash** terminal

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image18.png)

1. **Set permissions on the SSH key.** For macOS or Linux:

    `chmod 400 ~/Downloads/vmKey.pem`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image19.png)

1. **Connect to the VM. **Replace *{PUBLIC_IP}* with the public IP address returned during VM creation. If prompted to trust the host, type yes and press Enter.

    `ssh -i ~/Downloads/vmKey.pem azureuser@{PUBLIC_IP}`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image20.png)


### Step 4 — Install Java 11, Tomcat 9, and a database server

1. Install Java (verified Microsoft package):

    `sudo tdnf update -y`

    `sudo tdnf install -y java-25-openjdk tar gzip curl`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image21.png)

1. Install Tomcat 9 as a tarball (Azure Linux doesn't ship a native Tomcat package — double-check the version/URL against the current Apache archive, it drifts):

    `curl -fsSL https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.90/bin/apache-tomcat-9.0.90.tar.gz%20-o%20/tmp/tomcat.tar.gz`

    `ls -lh /tmp/tomcat.tar.gz`

    `sudo mkdir -p /opt/tomcat`

    `sudo tar xzf /tmp/tomcat.tar.gz -C /opt/tomcat --strip-components=1`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image22.png)

1. Verify tomcat insallation

    `sudo ls /opt/tomcat/bin`

1. Find and install the database package yourself — deliberately not hardcoded, since the exact name can vary by Azure Linux version:

    `sudo tdnf install -y mariadb-server`

    `sudo systemctl enable --now mariadb`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image23.png)


### Task 5 — Load sample data

1. A small dataset — enough to prove the app works, not the full public "world" dataset:

    `sudo mysql -u root`

    ```
    CREATE DATABASE world;
    USE world;
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image24.png)


    ```
    CREATE TABLE country (

    Code CHAR(3) NOT NULL PRIMARY KEY,

    Name CHAR(52) NOT NULL,

    Continent CHAR(13) NOT NULL,

    Region CHAR(26) NOT NULL,

    SurfaceArea DECIMAL(10,2) NOT NULL DEFAULT 0.00,

    IndepYear SMALLINT,

    Population INT NOT NULL DEFAULT 0,

    LifeExpectancy DECIMAL(3,1),

    GNP DECIMAL(10,2),

    GNPOld DECIMAL(10,2),

    LocalName CHAR(45) NOT NULL,

    GovernmentForm CHAR(45) NOT NULL,

    HeadOfState CHAR(60),

    Capital INT,

    Code2 CHAR(2) NOT NULL

    );

    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image25.png)


    ```
    CREATE TABLE city (

    ID INT NOT NULL AUTO_INCREMENT PRIMARY KEY,

    Name CHAR(35) NOT NULL,

    CountryCode CHAR(3) NOT NULL,

    District CHAR(20) NOT NULL,

    Population INT NOT NULL DEFAULT 0,

    FOREIGN KEY (CountryCode) REFERENCES country(Code)

    );

    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image26.png)


    ```
    INSERT INTO country VALUES

    ('USA','United States','North America','North America',9363520.00,1776,331000000,78.5,21430000.00,20580000.00,'United States','Federal Republic','Joe Biden',NULL,'US'),

    ('IND','India','Asia','Southern Asia',3287263.00,1947,1380000000,69.7,2870000.00,2660000.00,'Bharat','Federal Republic','Droupadi Murmu',NULL,'IN');

    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image27.png)


    ```
    INSERT INTO city (Name, CountryCode, District, Population) VALUES

    ('New York','USA','New York',8419000),

    ('Los Angeles','USA','California',3980000),

    ('Mumbai','IND','Maharashtra',12442000),

    ('Delhi','IND','Delhi',11034000);

    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image28.png)

1. Run the query `SELECT COUNT(*) FROM city;` and make sure it returns **4**.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image29.png)


### Task 6 — Build the WAR file locally (your own machine, not the VM)

1. Open new GitBash instance in VS code and clone the repo in your local VM

    `git clone https://github.com/yoshioterada/Java-WebApp-to-Tomcat-on-Azure-App-Service-Linux.git`

    `cd Java-WebApp-to-Tomcat-on-Azure-App-Service-Linux/java-webapp-with-mysql`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image30.png)

1. Open the cloned folder in VS code .Open pom.xml and replace maven-war-plugin Artifact version form 2.3 (@ line \#98) to +++3.4.0+++ and save the file.

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image31.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image32.png)

1. click on new Terminal-\> gitBash and run below command

    `mvn clean package`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image33.png)

1. Run the command to check for war file

    `ls target/*.war`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image34.png)

1. Update below command with Azure linux VM public id and run it to check target/azure-javaweb-app.war exists.

    `scp -i ~/Downloads/vmKey.pem target/azure-javaweb-app.war azureuser@{public-ip}:/tmp/`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image35.png)


### Step 7 — Deploy and configure the database connection

1. Run below command to connect to Azure Linux.Update below command with PublicIP and run it

    `ssh -i ~/Downloads/vmKey.pem azureuser@{public-ip}`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image36.png)

1. Run below command to deploy WAR to Tomcat

    `sudo cp /tmp/azure-javaweb-app.war /opt/tomcat/webapps/`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image37.png)


### Task 8- Configure the application database connection and start Tomcat

1. Connect to db and create application db user

    ```
    sudo mysql -u root

    CREATE USER 'appuser'@'localhost' IDENTIFIED BY 'Pass@word123';

    GRANT ALL PRIVILEGES ON world.* TO 'appuser'@'localhost';

    FLUSH PRIVILEGES;

    EXIT;
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image38.png)

1. Run below command to verify if user exists.

    `sudo mysql -u root -e "SELECT User,Host FROM mysql.user;"`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image39.png)

1. Create a the Tomcat environment configuration file named +++setenv.sh+++ in the Tomcat bin directory.

    ```
    sudo tee /opt/tomcat/bin/setenv.sh << 'EOF'
    export JDBC_DRIVER="com.mysql.cj.jdbc.Driver"
    export
    JDBC_URL="jdbc:mysql://localhost:3306/world?useSSL=false&serverTimezone=UTC"
    export DB_USER="appuser"
    export DB_PASSWORD="Pass@word123"
    EOF
    ```

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image40.png)

1. Run below command to make the file executable.

    `sudo chmod +x /opt/tomcat/bin/setenv.sh`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image41.png)

1. Run the below command to start the Tomcat application server.

    `sudo /opt/tomcat/bin/catalina.sh start`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image42.png)

### Step 7 — Verify end-to-end

1. Verify Storefront application -update below command with Azure Linux
    VM IP and run it in browser

    `http://{public-ip}:8080/azure-javaweb-app/`

    ![](https://raw.githubusercontent.com/technofocus-pte/azlnxdepth/refs/heads/main/Lab01/media/image43.png)
    
    >[!Note] The page loads, the left panel shows continents,
    > expanding North America shows United States, and selecting it lists
    > New York and Los Angeles on the right — the real assertion that VM,
    > runtime, Tomcat, MariaDB, and network all work together.

### Summary:

> In this lab, you provisioned an Azure Linux virtual machine and
> transformed it into a fully functioning application server. You
> installed Java, Tomcat, and MariaDB, created and populated a database,
> built and deployed a Java web application, configured its database
> connectivity, and validated successful access through a web browser.
