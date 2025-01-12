# **Task 7: Set Up a Load Balancer for Dockerized Applications**

This document provides a step-by-step guide for beginners to set up an Application Load Balancer (ALB) for Dockerized applications running on EC2 instances. The ALB will distribute traffic between the two instances created in **Task 5**.

---

## **Overview of Application Load Balancer**

An **Application Load Balancer (ALB)** is a service provided by AWS that automatically distributes incoming traffic across multiple targets, such as EC2 instances. It improves application availability and scalability by balancing the load between instances and monitoring their health.

---

## **Steps to Set Up the Load Balancer**

### **Step 1: Create an Application Load Balancer (ALB)**

1. **Navigate to the Load Balancer Section:**
   - In the **AWS Management Console**, go to the **EC2 Dashboard** and click on **Load Balancers** in the left-hand menu.
   - Click **Create Load Balancer** and choose **Application Load Balancer**.

2. **Configure the Load Balancer:**
   - **Name:** Provide a meaningful name for the ALB, e.g., `DockerizedApp-ALB`.
   - **Scheme:** Select **Internet-facing** to make the load balancer accessible from the internet.
   - **IP Address Type:** Choose **IPv4**.
   - **Listeners:** Ensure there is a listener configured for **HTTP** on port **80**.
   - **Availability Zones and Subnets:**
     - Select at least two subnets in different Availability Zones.
     - This ensures high availability for your application.

3. **Set Security Groups for ALB:**
   - Create or use an existing **Security Group** for the ALB with the following rules:
     - **HTTP (Port 80):** Allow traffic from anywhere (0.0.0.0/0).

4. **Review and Create:**
   - Review the configuration and click **Create Load Balancer**.

---

### **Step 2: Create a Target Group**

1. **Navigate to the Target Groups Section:**
   - In the EC2 Dashboard, click on **Target Groups** in the left-hand menu.
   - Click **Create Target Group**.

2. **Configure the Target Group:**
   - **Name:** Provide a meaningful name for the Target Group, e.g., `DockerizedApp-TG`.
   - **Target Type:** Select **Instances**.
   - **Protocol and Port:** Choose **HTTP** and **Port 80**.
   - **Health Checks:**
     - Set the **Health Check Protocol** to **HTTP**.
     - Set the **Health Check Path** to `/`.
     - Leave other settings as default for now.

3. **Create the Target Group:**
   - Click **Create Target Group** to complete the configuration.

---

### **Step 3: Register EC2 Instances with the Target Group**

1. **Select the Target Group:**
   - In the Target Groups section, select the Target Group you just created.
   - Click **Targets** > **Edit**.

2. **Add Instances to the Target Group:**
   - Select the two EC2 instances created in **Task 5** (running the Dockerized applications).
   - Click **Include as pending below**, then **Save**.

3. **Verify Instance Health:**
   - After registering, the ALB will start monitoring the health of the instances.
   - Wait until the instance health status changes to **Healthy**.

---

### **Step 4: Attach the Target Group to the Load Balancer**

1. **Navigate to the Load Balancers Section:**
   - In the EC2 Dashboard, select your ALB from the **Load Balancers** list.
   - Click on the **Listeners** tab.

2. **Edit Listener Rules:**
   - Under the HTTP listener, click **View/Edit Rules**.
   - Forward all HTTP traffic to the Target Group you created (`DockerizedApp-TG`).
   - Save the changes.

---

### **Step 5: Verify the Load Balancer**

1. **Obtain the ALB DNS Name:**
   - In the **Load Balancers** section, select your ALB and find its **DNS Name** in the **Description** tab.

2. **Access the Load Balancer:**
   - Open a browser and navigate to the ALB's DNS name, e.g., `http://<ALB-DNS-Name>`.

3. **Verify Traffic Distribution:**
   - Refresh the page multiple times to confirm the traffic is being distributed between the two EC2 instances. Each refresh should display content served by one of the instances.

---

## **Key Concepts for Beginners**

1. **Load Balancer:**
   - Distributes incoming traffic across multiple targets (e.g., EC2 instances) to ensure better availability and fault tolerance.

2. **Target Group:**
   - A group of instances or IP addresses that the Load Balancer routes traffic to.

3. **Health Checks:**
   - The ALB periodically checks the health of registered targets. If an instance becomes unhealthy, the ALB stops sending traffic to it.

---

## **Common Troubleshooting Tips**

1. **Instances Show as Unhealthy:**
   - Ensure the Dockerized application is running on port 80 in both instances.
   - Verify the Health Check Path (`/`) is correctly configured.

2. **Access Denied to Load Balancer:**
   - Check the ALB's Security Group to ensure it allows incoming traffic on port 80.

3. **Uneven Traffic Distribution:**
   - Confirm both instances are registered as healthy targets in the Target Group.

---

## **Summary**

By completing this task, you have:
- Configured an **Application Load Balancer (ALB)**.
- Registered two EC2 instances running Dockerized applications as targets.
- Verified the ALB distributes traffic evenly between the instances.
