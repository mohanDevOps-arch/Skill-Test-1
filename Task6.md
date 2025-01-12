# **Task 6: Create and Use a Custom AMI with Docker Pre-installed**

## **Part 1: Prepare the EC2 Instance**

### **Step 1: Use an Existing EC2 Instance**
- Select the EC2 instance you set up in **Task 5** with Docker and NGINX already installed and running.

### **Step 2: Configure Docker to Start on Boot**
1. SSH into the instance:
   ```bash
   ssh -i <your-key-file>.pem ec2-user@<instance-public-ip>
   ```
2. Enable Docker to start on boot:
   ```bash
   sudo systemctl enable docker
   ```

3. (Optional) Verify Docker and the previously deployed NGINX container:
   - Check if the NGINX container is still running:
     ```bash
     docker ps
     ```
   - If the container is running, access the NGINX welcome page in your browser using the public IP of the instance. For example: `http://<instance-public-ip>`.
   - This confirms that Docker is functioning properly. No need to run another `hello-world` container.

---

## **Part 2: Create a Custom AMI**

### **Step 1: Stop the EC2 Instance**
1. Go to the **EC2 Dashboard** in the AWS Management Console.
2. Select the configured EC2 instance.
3. Stop the instance:
   - Click **Actions > Instance State > Stop Instance**.
   - Confirm the action.

### **Step 2: Create an AMI**
1. Once the instance is stopped:
   - Select the instance in the EC2 dashboard.
   - Click **Actions > Image and templates > Create image**.
2. Provide the following details:
   - **Image Name:** A descriptive name, e.g., `Custom-Docker-AMI`.
   - **Description:** Add a description, e.g., "Custom AMI with Docker and NGINX pre-installed".
   - **Instance Volumes:** Verify or modify the instance volumes as needed.
3. Click **Create Image**.
4. Track the AMI creation progress:
   - Go to **Images > AMIs** in the EC2 dashboard.
   - Wait until the AMI status changes to **Available**.

---

## **Part 3: Launch a New EC2 Instance Using the Custom AMI**

### **Step 1: Select the Custom AMI**
1. Navigate to the **AMIs** section in the EC2 dashboard.
2. Locate your custom AMI.
3. Select the AMI and click **Launch Instance from Image**.

### **Step 2: Configure the New EC2 Instance**
1. **Instance Type:**
   - Choose the same instance type as the original EC2 instance or a compatible type.
2. **Network Settings:**
   - Launch the instance in a public subnet of your VPC.
   - Assign a **public IP address** to make the instance accessible externally.
3. **Security Groups:**
   - Use the same security group as in **Task 5**, which includes:
     - **SSH (Port 22):** Allow access from your IP.
     - **HTTP (Port 80):** Allow access from anywhere (0.0.0.0/0).
4. **Key Pair:**
   - Select the same key pair used for the original instance or generate/download a new one.

### **Step 3: Launch the Instance**
- Review the instance details and click **Launch**.

---

## **Part 4: Verify Docker Pre-installation**

### **Step 1: SSH into the New Instance**
1. Use the same key pair to connect:
   ```bash
   ssh -i <your-key-file>.pem ec2-user@<new-instance-public-ip>
   ```

### **Step 2: Verify Docker and NGINX**
1. Check if Docker is installed and running:
   ```bash
   docker --version
   sudo systemctl status docker
   ```
   - Ensure Docker is active and running.

2. Verify the NGINX container is running from the pre-installed configuration:
   ```bash
   docker ps
   ```
   - If the container is running, you should see the NGINX container listed. Open the browser and access it using the instance’s public IP (`http://<new-instance-public-ip>`).

3. (Optional) Restart the NGINX container if needed:
   ```bash
   docker start <nginx-container-id>
   ```

   - Alternatively, deploy a new container:
     ```bash
     docker run -d -p 80:80 --name nginx-container nginx
     ```

---

## **Summary**

### **What You Accomplished**
1. Configured an EC2 instance with Docker and NGINX pre-installed.
2. Created a custom AMI from this configured instance.
3. Launched a new EC2 instance using the custom AMI.
4. Verified that Docker and NGINX were pre-installed and running on the new instance.

### **Key Takeaways**
- **Custom AMIs Save Time:**
  - Automate repetitive tasks like installing Docker or setting up pre-configured environments.
- **Consistency Across Instances:**
  - Ensures all instances have the same setup, reducing configuration errors.
- **Leverage Existing Workflows:**
  - Build on previously verified setups (e.g., using the NGINX container) to save time and avoid unnecessary duplication.

This completes your custom AMI creation and deployment process. Let me know if you need further clarification!
