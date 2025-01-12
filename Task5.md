# Task 5: Deploy a Dockerized Web Application on EC2 Instances

## Overview

In this task, you will deploy a Dockerized web application on two EC2 instances. You will use the **host network** mode to ensure the application runs directly on the EC2 instance's public IP. This step-by-step guide is beginner-friendly and includes detailed explanations, particularly about the **host network** concept.

---

## Step 1: Launch Two EC2 Instances

### Instructions

1. **Create a VPC:**

   - Use the default VPC or create a custom VPC with at least one public subnet.
   - Ensure the subnet has access to the internet via an Internet Gateway.

2. **Launch EC2 Instances:**

   - Launch two EC2 instances in the public subnet.
   - Choose an Amazon Linux 2 AMI or Ubuntu as the instance type.
   - Ensure that **Auto-assign Public IP** is enabled.

3. **Security Groups Configuration:**

   - Create and attach a Security Group with the following inbound rules:
     - **SSH (Port 22):** Allow access from your IP address.
     - **HTTP (Port 80):** Allow access from anywhere (`0.0.0.0/0`).

4. **User Data Script:**

   - Add the following user data script during instance creation to automate Docker installation:
     ```bash
     #!/bin/bash
     sudo apt update -y
     sudo apt install -y docker.io
     sudo systemctl start docker
     sudo systemctl enable docker
     ```

---

## Step 2: Deploy a Dockerized Application

### SSH into EC2 Instances

1. Use the **public IP** of the EC2 instances to SSH into them:

   ```bash
   ssh -i <your-key.pem> ec2-user@<public-ip>
   ```

2. Confirm Docker is installed:

   ```bash
   docker --version
   ```

### Pull a Sample Docker Image

1. Use a lightweight sample image like Nginx:
   ```bash
   docker pull nginx
   ```

### Run the Docker Container with Host Networking

1. Start the Nginx container using the **host network**:
   ```bash
   docker run --network host nginx
   ```

---

## Step 3: Verify the Deployment

1. Open your web browser.
2. Access the **public IP address** of the EC2 instance:
   ```plaintext
   http://<public-ip>
   ```
3. You should see the default Nginx welcome page, confirming the application is running and accessible.

---

## **What is Host Networking?**

### **Explanation for Beginners**

- **Normal Docker Networking:**
  - Docker containers use **bridge networking** by default, where each container gets its own private IP address. 
  - Traffic is forwarded from the host's public IP and port to the container's private IP and port through Network Address Translation (NAT).

- **Host Network Mode:**
  - In host networking, the container directly uses the host machine's network stack.
  - Key Characteristics:
    - The container does not get its own IP address and uses the host's IP instead.
    - Applications in the container bind directly to the host's ports, eliminating the need for port mapping.
    - No network translation occurs, leading to slightly better performance.

### **Why Use Host Networking?**

1. **Simplified Access:**
   - Applications are directly accessible via the host machine's public IP without additional configurations.
2. **Improved Performance:**
   - Host networking avoids the overhead of routing traffic through Docker's virtual network.
3. **Specific Use Cases:**
   - Ideal for performance-critical applications or when direct access to host network resources is required.

---

## **Steps to Run Tomcat Using Docker Networking**

### **1. Pull the Tomcat Image**
   Download the official Tomcat image from Docker Hub:
   ```bash
   docker pull tomcat:9.0
   ```

---

### **2. Run Tomcat with Default Bridge Networking**
   Start the Tomcat container using port mapping:
   ```bash
   docker run -d -p 8080:8080 --name tomcat-container tomcat:9.0
   ```
   - Tomcat runs in bridge mode and is accessible at `http://<host-ip>:8080`.

---

### **3. Run Tomcat Using Host Networking**
   Before proceeding, **stop any application (like NGINX)** that uses the same port on the host to avoid conflicts. For example:

   Stop NGINX:
   ```bash
   docker stop nginx-container
   ```

   Start Tomcat with host networking:
   ```bash
   docker run --network host -d --name tomcat-host-network tomcat:9.0
   ```
   - In this mode, Tomcat uses the host's network stack directly and binds to port `8080` on the host.
   - Access Tomcat at `http://localhost:8080`.

---

### **Key Differences: Bridge Networking vs. Host Networking**

| **Aspect**              | **Bridge Networking**                                              | **Host Networking**                                    |
|-------------------------|--------------------------------------------------------------------|-------------------------------------------------------|
| **IP Address**          | Container has its own private IP address.                        | Uses the host's IP address directly.                 |
| **Port Mapping**        | Requires explicit port mapping (e.g., `-p 8080:8080`).            | No need for port mapping; binds directly to host ports. |
| **Use Case**            | Best for isolated and multi-container setups.                    | Ideal for single-container setups or performance-critical apps. |

---

### **Example Scenario: Why Stop NGINX?**
- If NGINX is running on the host and uses port `80`, starting another application (like Tomcat) with host networking on the same port will result in a **port conflict**. 
- Always ensure the required ports are free before running containers in host mode.

---

### **Final Verification**
1. Stop any conflicting containers (e.g., NGINX):
   ```bash
   docker stop nginx-container
   ```
2. Start Tomcat using host networking:
   ```bash
   docker run --network host -d tomcat:9.0
   ```
3. Open your browser and visit `http://localhost:8080` to confirm Tomcat is running.

---

### **Understanding Networking in Docker**
- **Bridge Networking:** Suitable for isolated containers or when running multiple containers on different ports.
- **Host Networking:** Best for single-container applications or when simplicity and performance are priorities.

This explanation helps learners grasp the difference between networking modes and demonstrates host networking with practical steps.
