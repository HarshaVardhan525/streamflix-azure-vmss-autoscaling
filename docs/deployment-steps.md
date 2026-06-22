# Streamflix Azure Deployment Guide

## Project Overview

This document outlines the step-by-step deployment process used to build a highly available Streamflix web application on Microsoft Azure using Virtual Machines, Custom Images, VM Scale Sets, Auto Scaling, and Load Balancer.

---

## Prerequisites

* Azure Subscription
* SSH Key Pair (.pem file)
* Ubuntu Virtual Machine
* Streamflix HTML Application

---

# Step 1: Create Resource Group

1. Sign in to the Azure Portal.
2. Navigate to Resource Groups.
3. Click Create.
4. Enter the following details:

| Setting             | Value                  |
| ------------------- | ---------------------- |
| Resource Group Name | Streamflix-RG          |
| Region              | Preferred Azure Region |

5. Click Review + Create.
6. Click Create.

---

# Step 2: Create Virtual Network

1. Navigate to Virtual Networks.
2. Click Create.
3. Configure the following:

| Setting       | Value           |
| ------------- | --------------- |
| VNET Name     | Streamflix-VNET |
| Address Space | 10.0.0.0/16     |

4. Create the Virtual Network.

---

# Step 3: Create Subnets

Create the following subnets inside Streamflix-VNET.

### Public Subnet

| Setting       | Value         |
| ------------- | ------------- |
| Name          | Public-Subnet |
| Address Range | 10.0.1.0/24   |

### Private Subnet

| Setting       | Value          |
| ------------- | -------------- |
| Name          | Private-Subnet |
| Address Range | 10.0.2.0/24    |

---

# Step 4: Create Ubuntu Virtual Machine

1. Navigate to Virtual Machines.
2. Click Create.
3. Select Ubuntu Server.
4. Place the VM inside Streamflix-VNET.
5. Select the Public Subnet.
6. Configure SSH authentication using a key pair.
7. Create the VM.

---

# Step 5: Connect to Virtual Machine

Open Command Prompt or PowerShell on the local machine.

```bash
ssh -i .\key.pem ubuntu@<Public-IP>
```

Switch to root user:

```bash
sudo su -
```

---

# Step 6: Install Apache Web Server

Update package repositories:

```bash
apt update
```

Install Apache2:

```bash
apt install apache2 -y
```

Verify Apache status:

```bash
systemctl status apache2
```

---

# Step 7: Deploy Streamflix Application

Navigate to Apache web root directory:

```bash
cd /var/www/html
```

Open the default page:

```bash
vi index.html
```

Delete existing content:

```bash
:%d
```

Paste the Streamflix HTML source code.

Save and exit:

```bash
:wq
```

Verify application deployment:

```bash
http://<Public-IP>
```

The Streamflix application should now be accessible from the browser.

---

# Step 8: Generalize the Virtual Machine

Prepare the VM for image creation:

```bash
waagent -deprovision+user
```

Type:

```bash
y
```

to confirm deprovisioning.

---

# Step 9: Stop the Virtual Machine

1. Navigate to the Virtual Machine.
2. Click Stop.
3. Wait until the VM status changes to Stopped (Deallocated).

---

# Step 10: Capture Custom Image

1. Open the Virtual Machine.
2. Select Capture.
3. Configure:

| Setting    | Value            |
| ---------- | ---------------- |
| Image Name | Streamflix-Image |

4. Create the custom image.

The image will be used for future VM deployments.

---

# Step 11: Create VM Scale Set

1. Navigate to Virtual Machine Scale Sets.
2. Click Create.
3. Select Streamflix-Image as the source image.
4. Attach the scale set to Streamflix-VNET.
5. Select the Public Subnet.
6. Enable scaling options.

Create the VM Scale Set.

---

# Step 12: Configure Auto Scaling

Configure automatic scaling rules.

### Scale Out Rule

| Metric          | Value                   |
| --------------- | ----------------------- |
| CPU Utilization | Greater than 50%        |
| Action          | Increase Instance Count |

### Scale In Rule

| Metric          | Value                   |
| --------------- | ----------------------- |
| CPU Utilization | Less than 20%           |
| Action          | Decrease Instance Count |

### Capacity Settings

| Setting           | Value |
| ----------------- | ----- |
| Minimum Instances | 1     |
| Desired Instances | 1     |
| Maximum Instances | 2     |

Save the scaling configuration.

---

# Step 13: Configure Azure Load Balancer

1. Create an Azure Load Balancer.
2. Associate the VM Scale Set backend pool.
3. Configure:

   * Frontend IP
   * Backend Pool
   * Health Probe
   * Load Balancing Rule

Save the configuration.

---

# Step 14: Test the Application

Access the application using the Load Balancer Public IP Address:

```text
http://<Load-Balancer-Public-IP>
```

Verify:

* Streamflix application loads successfully.
* Load Balancer distributes traffic.
* Auto Scaling rules are active.
* VM Scale Set instances are healthy.

---

# Project Outcome

Successfully deployed a highly available Streamflix web application on Microsoft Azure using:

* Azure Resource Group
* Azure Virtual Network
* Public and Private Subnets
* Ubuntu Virtual Machine
* Apache Web Server
* Custom Azure Image
* Azure VM Scale Set
* Auto Scaling
* Azure Load Balancer

The architecture supports scalability, high availability, and efficient resource utilization through automatic scaling based on CPU utilization.
